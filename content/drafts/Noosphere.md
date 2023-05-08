---
author: "Daniel Jackson"
title: "Noosphere concepts"
date: "2023-05-02"
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

This is an attempt to apply the concept design framework of [EOS](https://essenceofsoftware.com) to Gordon Brander’s [Noosphere](https://subconscious.substack.com/p/noosphere-a-protocol-for-thought). Because I don’t understand Noosphere well, it will no doubt contain many errors, but it seemed like a fun and potentially useful exercise, both as a test for concept design and perhaps also to offer a different perspective to the design of Noosphere itself.

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

**Design question: minimality**. The boring operational principle suggests that maybe there should be more to a notebook than this. Am I missing something?

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

**Design question: mutability**. This concept introduces a type *Note* of immutable notes. The type *Text* is assumed to be the standard, mutable type of textual strings. The concept attempts to define the basic functionality needed for creating immutable notes incrementally; the idea is to introduce a mutable *Buffer* type for editing, which produces immutable notes on saving.

Noosphere uses the conventional concept of content-addressable storage for naming notes, which I’ll attempt to define in a concept:

	concept ContentAddress
	purpose name objects by content
	principle
	  // if you hash an object, you can find it later
	  // with the resulting address
	  hash(t, a); get(a, t’) {t’ = t}
	state
	  address: Text lone -> one static Address
	actions
	  hash (t: Text, out a: Address)
	    // compute a new address and store it
	  get (a: Address, out t: Text)
	    // return text associated with an address
	  forget (a: Address)
	    // forget text associated with an address

**Design question: purpose**. I am not sure how to express the purpose, that is what motivates this concept. It seems to be a combination of a naming scheme that works across storage sites (since an object implicitly carries its own name), and a kind of built in authentication (since modifying of corrupting an object will change its content and thus its name).

**Design question: collisions**. The state declaration says that each text maps to *one* address; that each address is mapped to by at most one (*lone*) text (asserting that collisions never happen). The mapping from text to address is *static*, so the address of a text cannot change. (Strictly speaking, the semantics of *static* might allow a text to be assigned an address, be forgotten, and then be assigned a different address. This might model a change of hash algorithm.)

**Design question: content type**. I’m assuming for now that the contents are text, since this is easily compatible with the *Textnote* concept introduced before. Obviously content addressing could apply to a lower level representation and could handle other media types uniformly.

**Design question: indexing**. The concept assumes some kind of index that comprises the state; at the moment, I’m not concerned with how this index might be localized, but will just assume it’s available globally. Given this, the role of the *forget* action isn’t clear.

**Design question: notes with same content**. Anticipating the concept composition, I’m wondering what will happen if you save a buffer whose text content is the same as that of an existing note. I suppose what will happen is that no new note will be created, and instead the new shorthand name that you provide for the note will be bound to the existing note (in addition to any existing shorthand names). There’s a slight snag here because the *Textnote* concept says a new note is create on save, but it won’t be true in this case.

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

**Design question**. I’ve declared the key/principal relations to be static in both directions, which means that (a) you can’t assign a new key to an existing principal, and (b) you can’t assign a new principal to an existing key (even its principal no longer exists). If not, Noosphere would break because references to notebooks would change in meaning over time. Notebook owners will have to make sure they run key generation just once and look after their private keys carefully!

Content addressable names and public key names are not mnemonic, so Noosphere introduces user-defined shorthands:

	concept Shorthand [Context, Object]
	state
	  resolve: Context, Short -> one Object
	actions
	  createContext (out c: Context)
	    // create new context for resolving shorthands
	  deleteContext (c: Context)
	    // remove context and all its shorthands
	  createShorthand (c: Context, o: Object, out s: Short)
	    // store new shorthand for object o in context c
	  deleteShorthand (c: Context, s: Short)
	    // remove shorthand s from context c
	  lookup (c: Context, s: Short, out o: Object)
	     // remove shorthand s from context c

**Design issue: uniqueness**. In a given context, a shorthand must name a unique object but the same shorthand can name different objects in different contexts. Shorthands are not canonical: the same object can have multiple shorthand names, even in the same context.

**Design issue: mutability**. The shorthand mapping is not static: a shorthand can name different objects at different times. In fact, this will be essential in Noosphere, since this will allow a shorthand to refer to the latest version of a note.

**Technical issue: naming names**. The target of a shorthand, represented by the type variable *Object*, may itself be a name in another naming concept. 

**Technical issue: shorthand name type**. The *Short* type is abstracted here, but will usually just be a text string.

**Technical issue: allocating contexts**. The concept is generic in *Context*, anticipating the decision that a notebook will be a context. This means that some clever synchronization will be needed, so that a context doesn’t get allocated in two concepts at once.

Noosphere has a simple kind of versioning, in which notes can point to their prior versions. I’ll model that with a generic versioning concept:

	concept Version [Object]
	  purpose access to and restoring of old versions
	  principle
	    // if you register p as previous version of o,
	    // then you'll get p if you ask for o's previous
	    register (o, p); getPrev (o, p') {p' = p}
	  state
		  previous: Object -> lone Object
	  actions
	    register (o, prev: Object)
		    // record that prev is previous version of o
	    getPrev (o: Object, out prev: Object)
	      // return previous version of o (it exists)

**Design issue: invariants**. Each object can have at most one predecessor, but potentially any number of successors. Cycles aren’t permitted; in particular, an object can’t be its own previous version.

**Design issue: interaction with content addressing**. Suppose I have a note N1 and create a new version N2, but N2 happens to have the same content as N0, which already has its own previous version. What happens now?

## Composing concepts

Notebook [Textnote.Note]
Textnote
ContentAddress [??]
PublicKeyName [Principal]
Shorthand [Notebook.Book, Textnote.Note]
Shorthand [Notebook.Book, Notebook.Book]
Version [Textnote.Note]

**To resolve**: 
- saving to note when context matches existing note
- allocating contexts

To do
- add objects to content address

Questions. Can the previous version belong to another notebook? Why must shorthand contexts be associated with notebooks?