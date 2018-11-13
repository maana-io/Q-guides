---
description: Multiple Mutations in one Query
---

# Graph Mutations

### Graph Mutations

#### Multiple Mutations in One Query

The following illustrates how to perform multiple mutations in one query:

`mutation {  
  # Mutation 1  
  updateCurrentUser(input: {  
    userPatch: {  
      website: "https://twitter.com/benjie"  
    }  
  }) {  
     user { website }  
  }   
  #Mutation 2  
  setGreeting(input: {  
    newGreeting: "Hello Universe!"  
  }) {  
     greeting  
  }  
}`





