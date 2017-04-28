- Start Date: 2017-04-26
- RFC PR: (leave this empty)
- Ember Issue: (leave this empty)

# Summary

This RFC proposes a change to the semantics of the `findHasMany` and `findBelongsTo` hooks in ember-data adapters.

https://github.com/emberjs/data/issues/2162

# Motivation

Currently, async relationships are resolved through the adapter only if one of the two following scenarios is true:

1. The payload for the current model already has loaded ids for this relationship

   <details>
   <summary>For example, in JSONAPI</summary>

   ```json
   {
     "data": {
       "id": 1,
       "type": "post",
       "attributes": {},
       "relationships": {
         "comments": {
           "data": [
             { "type": "comment", "id": "2" },
             { "type": "comment", "id": "3" }
           ]
         }
       }
     }
   }
   ```

   </details>

2. The payload for the current model has a link for this relationship

   <details>
   <summary>For example, in JSONAPI</summary>

   ```json
   {
     "data": {
       "id": 1,
       "type": "post",
       "attributes": {},
       "relationships": {
         "comments": { "links": { "related": "/posts/1/comments" } }
       }
     }
   }
   ```

   </details>

However, if neither of these are present, accessing an async relationship will not call any hook in the adapter.

Because of this, a common workaround is to use the adapter to inject a link.

<details>
<summary>Example workaround</summary>

```javascript
normalize(typeClass, hash) {
  let url = `/posts/${hash.id}/comments";
  hash.relationships.comments = { "links": { "related": url } };
  return this._super(...arguments);
}
```

</details>

Enabling this hook would extend the possibilities for customizing ember-data adapters and enable more patterns for loading related data through relationships.

# Detailed design

This is the bulk of the RFC. Explain the design in enough detail for somebody
familiar with the framework to understand, and for somebody familiar with the
implementation to implement. This should get into specifics and corner-cases,
and include examples of how the feature is used. Any new terminology should be
defined here.

# How We Teach This

What names and terminology work best for these concepts and why? How is this
idea best presented? As a continuation of existing Ember patterns, or as a
wholly new one?

Would the acceptance of this proposal mean the Ember guides must be
re-organized or altered? Does it change how Ember is taught to new users
at any level?

How should this feature be introduced and taught to existing Ember
users?

# Drawbacks

Why should we *not* do this? Please consider the impact on teaching Ember,
on the integration of this feature with other existing and planned features,
on the impact of the API churn on existing apps, etc.

There are tradeoffs to choosing any path, please attempt to identify them here.

# Alternatives

What other designs have been considered? What is the impact of not doing this?

This section could also include prior art, that is, how other frameworks in the same domain have solved this problem.

# Unresolved questions

Optional, but suggested for first drafts. What parts of the design are still
TBD?
