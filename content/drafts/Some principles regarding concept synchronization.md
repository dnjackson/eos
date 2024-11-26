# Some notes about syncs

Daniel Jackson\
Sept 16, 2024

## Separating Semantic Actions from HTTP Request

All web app frameworks separate details of HTTP requests (HTTP verb, URL, serialization formats) from underlying data model calls. In our previous architecture (the one we used in Kodless and in the 6.1040 starter code), we used the HTTP “route” (that is, the endpoint API call corresponding to the HTTP request) also as the function that performs concept synchronization. 

This was a good tradeoff for the class because it closely mimics traditional app structure (which doesn’t have synchronizations), but it mixes the two concerns. Instead, we should have a synchronization layer that is separate from the HTTP layer, with each HTTP endpoint making only a single call to the concept sync layer.

## The Need for Additional Actions

In some cases, the HTTP endpoint might be able to call a concept action directly. But often the concept actions will require arguments that are provided by sync with other concepts. 

For example, consider an endpoint for creating a new comment. The Comment concept might have an action with the signature

	Comment.new (author: User, post: Post, content: String, out c: Comment)

(The types User and Post would actually be type parameters, but we can ignore that complication for now.)

One could imagine an app in which comment authors aren’t authenticated. In that case, the User type might be just a string, and the HTTP request might pass that on directly. But in a typical app, the author will be the user in the session, obtained by sync with a Session concept. To achieve that, we define a new interface action

	newComment (session: Session, post: Post, content: String)

in which the author is no longer specified (and the resulting comment object is not used).

This could then be defined by a synchronization:

	sync
		when newComment (s, p, c)
		u = Session.getUser (s)
    c = Comment.new (u, p, t)

## Modularity of Synchronizations

In our previous thinking, it was fine to define synchronizations in a completely application-specific way. That is, we consider the application at hand, and ask which actions amongst all its concepts should happen together.

But in the framework we’re developing, there will be a catalog of standard synchronizations for the user to choose from. These will often connect concepts pairwise. Syncs that involved more than two concepts would not work in some applications because they might require the presence of concepts that are not included. 

For example, one application might have notifications, so would want to sync Comment with Notification; another might have karma points, and want to sync Comment with Karma. These should be separate syncs so they can be selected independently of one another.

Here’s an example of a sync for notifications

	sync
		when Comment.new (u, p, t)
		Notification.new (u, "your new comment: " + t)
		Notification.new (p.author, "new comment on your post: " + t)

Note that the sync requires access to the state of the concepts (obtaining the author of the target post as the subject of the notification).

This sync illustrates also the chaining of synchronizations: the transaction would begin with the user executing newComment, which then fires Comment.new, which itself then fires Notification.new.

