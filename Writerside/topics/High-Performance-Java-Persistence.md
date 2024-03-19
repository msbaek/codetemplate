# High Performance Java Persistence

## 10. Relationships

![table-relations.png](../images/table-relations.png)

### JPA defines four association mapping constructs:

_ @ManyToOne represents the child-side (where the foreign key resides) in a database one-to-many table relationship.
_ @OneToMany is associated with the parent-side of a one-to-many table relationship.
_ @ElementCollection defines a one-to-many association between an entity and multiple value types (basic or embeddable).
_ @OneToOne is used for both the child-side and the parent-side in a one-to-one table relationship.
_ @ManyToMany mirrors a many-to-many table relationship.’

### 10.2 @ManyToOne

- The @ManyToOne relationship is the most common’
- foreign key is controlled by the child-side, no matter the association is unidirectional or bidirectional.
- unidirectional @ManyToOne

![hp-jp-10.2.png](hp-jp-10.2.png)

![hp-jp-10.3.png](hp-jp-10.3.png)

### 10.3 @OneToMany
- @ManyToOne이 most natural mapping
- @OneToMany can also mirror this database relationship, but only when being used as a bidirectional mapping
- unidirectional @OneToMany association uses an additional junction table, which no longer fits the one-to-many table relationship semantics.

#### 10.3.1 Bidirectional @OneToMany
- bidirectional @OneToMany association has a matching @ManyToOne child-side mapping

![hp-jp-10.4.png](hp-jp-10.4.png)

- In a bidirectional association, only one side can control the underlying table relationship.
- it is the child-side @ManyToOne association in charge of keeping the foreign key column value in sync with the in-memory Persistence Context
- This is the reason why the bidirectional @OneToMany relationship must define the mappedBy attribute, indicating that it only mirrors the @ManyToOne child-side mapping
- bidirectional association must always have both the parent-side and the child-side in sync.
- To synchronize both ends, it is practical to provide parent-side helper methods that add/remove child entities.
```java
public void addComment(PostComment comment) {
    comments.add(comment);
    comment.setPost(this);
}

public void removeComment(PostComment comment) {
    comments.remove(comment);
    comment.setPost(null);
}
```
- major advantages of using a bidirectional association 
  - entity state transitions can be cascaded from the parent entity to its children.
- In the following example, when persisting the parent Post entity, all the PostComment child entities are persisted as well.
```java
Post post = new Post("First post");
post.addComment(new PostComment("My first review"));
post.addComment(new PostComment("My second review"));
entityManager.persist(post);
```
- ‘When removing a comment from the parent-side collection:
`post.removeComment(comment1);`
- ‘The orphan removal attribute instructs Hibernate to generate a delete DML statement on the targeted child entity:
`DELETE FROM post_comment WHERE id = 2’`

  --- image path 변경