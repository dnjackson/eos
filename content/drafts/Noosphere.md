---
author: "Daniel Jackson"
title: "Noosphere concepts"
date: "2023-05-09"
description: "A Concept Exploration of Noosphere"
ShowToc: true
TocOpen: true
hideMeta: false # removes date etc from post
summary: "A Concept Exploration of Noosphere"
editPost:
    URL: "https://forum.softwareconcepts.io"
    Text: "Comments" # edit text
    appendFilePath: false # to append file path to Edit link
---

## Intro

This is an attempt to apply the concept design framework of [EOS](https://essenceofsoftware.com) to Gordon Brander’s [Noosphere](https://subconscious.substack.com/p/noosphere-a-protocol-for-thought). Because I don’t understand Noosphere well, it will no doubt contain many errors, but it seemed like a fun and useful exercise, both as a test for concept design and perhaps also to offer a different perspective to the design of Noosphere itself.

## Identifying concepts

The first step is to identify a collection of concepts that together can reproduce Noosphere’s basic functionality. Rather than starting with the concepts that Noosphere highlights (notably the central *Sphere* concept), I’ve followed the strategy of identifying concepts bottom-up, trying to be as granular as possible, and whenever possible reusing existing concepts that are domain independent.

After identifying a concept, I attempt to describe it using the standard EOS [structure](https://essenceofsoftware.com/posts/distillation/#a-structure-for-describing-concepts): a *name*, a *purpose* for the concept, an *operational principle* (an archetypal scenario explaining how it’s used), a *state* schema, and some *actions*.

Crucially, concepts can be generic, defined over some type variable (given after the name) which is bound when the concept is instantiated in a given design context.

The most basic concept seems to be the concept of a *Notebook* that is little more than a collection of notes:

	concept Notebook [Note]
	purpose
	  manage collection of owned notes
	principle
	  // if you create a notebook and add a note
	  // the note belongs to the notebook.
	  create(b); add(b, n) {n in b.notes}
	state
	  notes: Book one -> set Note
	actions
	  create (out b: Book)
	    // create new book b
	  delete (b: Book)
	    // delete book b and its notes
	  add (b: Book, n: Note)
	    // add note n to book b
	  remove (b: Book, n: Note)
	    // remove note n from book b

**Related concepts**. *Notebook* is similar to *Folder*, although notebooks usually can’t be nested. In contrast to *Label* (used, eg, to organize messages in Gmail) and *Collection* (used to organize photos in Lightroom), a note usually cannot belong to more than one notebook.

**Design question: sharing**. Can notes be shared between users? The *Notebook* concept does not allow a note to be shared between notebooks. A single notebook could be owned by multiple users, however. We might add an *Ownership* concept later. 

**Design question: mutability**. Notebooks are mutable, but the concept does not commit to whether notes themselves are. In most existing apps (such as Notion), notes are indeed mutable, but they won’t be in Noosphere.

**Design question: genericity**. The *Notebook* concept allows any type of note, but in practice this will be restricted to some standard media type (text, rich text, image, audio, movie, etc).

**Design question: minimality**. The boring operational principle suggests that maybe there may be more to a notebook than this. Am I missing something?

Now we turn to the notes themselves, and for now consider only a concept for textual notes:

	concept Textnote
	purpose provide editable notes with textual content
	principle
	  // if you open a buffer, edit some text in it,
	  // and save the buffer to a note, then the note
	  // contains the text
	  open(b); edit(b,t); save(b, n) {n.content = t}
	state
	  content: Note -> one static Text
	  current: Buffer -> one Text
	actions
	  open (out b: Buffer)
	    // create a new buffer with empty text content
	  open (n: Note, out b: Buffer)
	    // create new buffer with text content of existing note
	  edit (b: Buffer, t: Text)
	    // update content of buffer b to be t
	  save (b: Buffer, out n: Note)
	    // create new note with content matching current content of buffer
	  close (b: Buffer)
	    // discard buffer and association with current text

**Related concepts**. *Textnote* is similar to the *File* concept in GitHub’s web interface: when you open and edit a file in a repo, you’re modifying a buffer which you can then commit to create a new version of the file.

**Design question: mutability**. This concept introduces a type *Note* of immutable notes. The concept attempts to define the basic functionality needed for creating immutable notes incrementally; the idea is to introduce a mutable *Buffer* type for editing, which produces immutable notes on saving.

Noosphere uses the conventional concept of content-addressable storage for naming notes, which I’ll attempt to define in a concept:

	concept ContentAddress [Object]
	purpose name objects by content
	principle
	  // if you create an object, and later get an 
	  // object with the same address, it will 
	  // have the same content
	  create(o, a); get(a, o') {o’.content = o.content}
	state
	  address: Object -> one static Address
	  content: Object -> one static Text
	actions
	  create (o: Object, t: Text, out a: Address)
	    // create object o with contents t and
	    // compute a new address and store it
	  get (a: Address, out o: Object)
	    // return any object with address a
	  forget (a: Address)
	    // forget text associated with an address

**Design question: purpose**. I am not sure how to express the purpose, that is what motivates this concept. It seems to be a combination of a naming scheme that works across storage sites (since an object implicitly carries its own name), and a kind of built-in authentication (since modifying of corrupting an object will change its content and thus its name).

**Design question: copies**. The concept makes explicit that there may be several objects that are copies of the same content and that therefore have the same name. The *get* action is undetermined by design: it doesn’t specify which object is returned when more than one with the right address is available.

**Design question: immutability**. Note that the *address* and *content* relations are immutable: the address is computed by a hash function that is assumed not to change, and the content is not permitted to change (since otherwise the address would no longer match the contents).

**Design question: collisions**. The operational principle actually relies on there being no hash collisions. This is a reasonable assumption in practice.

**Design question: content type**. I’m assuming for now that the contents are text, since this is compatible with the *Textnote* concept introduced before. Obviously, content addressing could apply to a lower level representation and could handle other media types uniformly.

**Design question: indexing**. The concept assumes some kind of index (or cache) that comprises the *address* component of the state; at the moment, I’m not concerned with how this index might be localized, but will just assume it’s always available. Given this, the role of the *forget* action isn’t clear.

In Noosphere, notebooks are named by public keys. The purpose of this, presumably, is to avoid having a global directory mapping principals or notebooks to public keys: instead they just carry their public keys in their names.

	concept PublicKeyName [Principal]
	purpose simplify key distribution
	principle
	state
	  public, private: Principal one static -> one static Key
	actions
	  generate (p: Principal, out pr, pu: Key)
	    // generate new key pair for principal
	  find (k: Key, out p: Principal)
	    // return principal with public key k
	  sign (t: Text, p: Principal, out s: Text)
	    // sign text t with private key of p
	  check (t: Text, p: Principal, s: Text, out valid: Bool)
  	  // with public key of p check signature s matches t

**Technical issue**. Because concepts express state so abstractly (as relations), the difference between the key/principal relationship being in a global table and being carried by (a representation of) the principal itself isn’t made. This is a bit weird. For now, I’m not sure how to express the fact that the key is a name, except to provide an action to find a principal given a key, which is the quintessential naming lookup (and would presumably be missing in a key directory).

**Design question**. I’ve declared the key/principal relations to be static in both directions, which means that (a) you can’t assign a new key to an existing principal, and (b) you can’t assign a new principal to an existing key (even its principal no longer exists). If not, Noosphere would break because references to notebooks would change in meaning over time. Notebook owners will have to make sure they do key generation just once and save their private keys carefully!

Content-addressable names and public-key names are not mnemonic, so Noosphere introduces user-defined shorthands:

	concept Shorthand [Context, Object]
	purpose mnemonic, local naming of objects
	principle
	  // if you bind a short to an object
	  // you can then look it up with that short
	  bind (c, o, s); lookup (c, s, o') {o = o'}
	state
	  resolve: Context, Short -> one Object
	actions
	  bind (c: Context, o: Object, out s: Short)
	    // set shorthand for o in context c to be s
	    // remove bindings for existing objects named s in c
	  unbind (c: Context, s: Short)
	    // remove shorthand s from context c
	  lookup (c: Context, s: Short, out o: Object)
	    // return object with shorthand s in context c

**Design issue: uniqueness**. In a given context, a shorthand must name a unique object but the same shorthand can name different objects in different contexts. Shorthands (unlike content addresses) are not canonical: the same object can have multiple shorthand names, even in the same context.

**Design issue: mutability**. The shorthand mapping is not static: a shorthand can name different objects at different times. In fact, this will be essential in Noosphere, since this will allow a shorthand to refer to the latest version of a note.

**Technical issue: naming names**. The target of a shorthand, represented by the type variable *Object*, may itself be a name in another naming concept. 

**Technical issue: shorthand name type**. The *Short* will usually just be a text string, but since no properties of the name beyond equality are assumed, the type is abstracted here.

Noosphere has a simple kind of versioning, in which notes can point to their prior versions. I’ll model that with a generic versioning concept:

	concept Version [Object]
	  purpose access to and restoring of old versions
	  principle
	    // if you register p as previous version of o,
	    // you'll get p if you ask for o's previous version
	    register (o, p); getPrev (o, p') {p' = p}
	  state
		  previous: Object -> lone Object
	  actions
	    register (o, prev: Object)
		    // record that prev is previous version of o
	    getPrev (o: Object, out prev: Object)
	      // return previous version of o (it exists)

**Design issue: invariants**. Each object can have at most one predecessor, but potentially any number of successors. Cycles aren’t permitted; in particular, an object can’t be its own previous version.

## Composing concepts

Now we can assemble the concepts by composition. The first step is to instantiate the concepts, binding their type parameters to domain-specific types:

	app Noosphere
	include
		Textnote
		Notebook [Textnote.Note]
		ContentAddress [Textnote.Note]
		PublicKeyName [Notebook.Book]
		Shorthand [Notebook.Book, Textnote.Note]
		Shorthand [Notebook.Book, Notebook.Book]
		Version [Textnote.Note]

These inclusions say that the text notes of the *Textnote* concept comprise the notes of the *Notebook* concept and the objects of the *ContentAddress* concept. The *PublicKeyName* concept is applied to the books of the *Notebook* concept: that is, each notebook has its own public key.

The *Shorthand* concept is instantiated twice, to introduce shorthands for notes and for books. In both cases, the context is a notebook, the idea being that each notebook interprets the names within its notes. If a note in my notebook includes the name *@gordon/composability*, this will be resolved by finding the public key associated with *@gordon*, and then obtaining within the notebook with that public-key name the note whose content-address is associated with *composability*.

The second step is to synchronize the actions of the concepts. Let’s focus on a few of the more interesting ones. 

Here’s an attempt at explaining what happens when a note is saved:

	sync save_note (
		b: Notebook.Book,
		x: Textnote.Buffer,
		s: Short,
		prev: Textnote.Note,
		out n: Textnote.Note,
		out t: Text,
		out a: ContentAddress.Address)
		
		when t = Textnote.current[x]
		  and prev in Notebook.notes[b]
		Textnote.save (x, n)
		Notebook.add (b, n)
		ContentAddress.create (n, t, a)
		Shorthand.bind (b, a, s)
		Version.register (n, prev)

The arguments of the action include the book to which the note is being saved; the buffer being edited, a shorthand name being assigned to the new note, and the identity of the previous version of the note.

When the note is saved, the following actions occur in each of the concepts:

- In *Textnote*: the content of the buffer is saved to a note;
- In *Notebook*: the resulting note is added to the book;
- In *ContentAddress*: a new address is computed for the note;
- In *Shorthand*: the shorthand is bound to the new note (and, if used for previous versions, unbound);
- In *Version*, the new note is registered as the new version of the previous note.

**Design question: latest version naming**. Assuming that the user of this notebook reuses the same shorthand name for all versions of this note, an external user requesting a note with a given shorthand name will now acquire a copy of the new note. This seems right.

**Design question: content addresses not distinct**. If the text in the buffer happens to be the same as the text in an existing note in this notebook, the new note that is created will have the same content address as the existing note. Two distinct notes (with different version histories) will have the same content address name. Presumably this isn’t a problem, since a request for a note from an external user will provide only its content, and will not reveal the identity of the note or its version history. That is, content addresses aren’t canonical for the local notes in a notebook, but only for the cache of notes obtained externally.

**Design question: cross-notebook versioning**. The precondition requires the previous version of the note to belong to the same notebook. The alternative seems like trouble, because version tracking requires notebook identities, which are hidden outside a notebook.

**Design question: unstable shorthands**. We haven’t specified the relevant syncs for this, but I’m assuming that a notebook does not hold, in its shorthand context, the bindings for external notes. This means that the meaning of a reference appearing in a note in Alice’s notebook to a note in Bob’s notebook may change when Bob rebinds the shorthand. If Alice wants to avoid this, presumably she could reference the note by its content address (you could imagine an action that lets you specify the current shorthand for a link but then inserts the relevant content address). Another possibility would be that a notebook has a shorthand context not only for external notebooks, but also for notes in those notebooks. Then shorthands would be stable but would not get automatic version updates.

**Design question: local resolving of shorthands**. When a reference in a note obtained from an external notebook is resolved, is it cached locally? Presumably this means that different clients in a P2P network will disagree on the meaning of a shorthand?

**Design question: notebook as shorthand context**. It is not necessary that the notebook be the context for shorthands; there could be multiple contexts per notebook, or multiple notebooks per context. The notion of a “sphere” that combines notebook and shorthand context seems like the right default.

## Reflections

This exploration suggests some opportunities and issues for concept design (CD):

- **Decoupling.** CD seems to offer a nice way to decouple the elements of an intricate design like Noosphere, and to identify and incorporate the relevant preexisting concepts.
- **Catalog**. Defining the preexisting concepts was pretty hard, reflecting partly my own lack of knowledge, but also the subtlety of some of these concepts. This whole exercise makes it clear to me how valuable a catalog would be, so that a designer could just look up the concepts, learn about them, incorporate the right variants, and not have to figure them out.
- **More and less well-documented concepts**. Some of the concepts, such as *ContentAddress*, correspond to technology ideas that have been extensively described but not formulated as pithy concepts. Others correspond to ideas that must be well known to some but haven’t been widely articulated. *Textnote*, for example, tries to represent a simple scheme for an editing process that gives the appearance of a mutable document but produces a sequence of immutable instances. (This suggests I should probably have focused the concept purely on that, factored out the textual aspect and made it generic.)
- **Synergy**. Some of the compositions are probably resulting in [design synergies](https://essenceofsoftware.com/posts/distillation/#concept-synergy) that it would be good to explore. Using the Sphere for bounding the notebook, providing a context for shorthands, and carrying a public key is presumably synergistic in ways it would be good to figure out. Maybe public key naming should be described as a synergistic composition of a generic naming scheme and a naming-independent PKI concept.
- **Allocation**. Deciding when to have actions for allocation of objects is tricky, and I wanted to have concepts that could both allocate their own objects and compose with other concepts that would do the allocation instead. (This issue arose for allocating contexts in *Shorthand*: originally I had an allocation action in the concept itself, but then wasn’t sure how to treat the notebook, which is allocated elsewhere, as a context.) Perhaps the solution to this is to find a different way to model allocation of objects in concepts, maybe with some simple implicit default, so that it doesn’t need to be considered in most concept definitions.
- 