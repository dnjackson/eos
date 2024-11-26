function cascadeDel (p: PostConcept, c: CommentConcept) {
return i -> {
p.del (*, i);
c.del (i)
}
}

function authDel (p: PostConcept, s: SessionConcept) {
return i -> {
u = s.getUser ();\
p.del (u, i);
}
}

function authCascDel (...)

sy = new Sync ();\
sy = sy.add (\
new Link (“Post.del”,”Comment.del”)
)
sy = sy.add (\\
// when Sess.user, Post.del (u)
)

