# REACT-REDUX-Complex-State

## Strategies for Complex State
- A REDUX store helps centralize, organize features and data. --> Complex Store State
- Redux is suited for complex applications with features each with state data to manage.
- Strategies can help manage a complex store state (State Management).
- REDUX one-way data flow --> State → View → Actions → State → View
- To design a store’s state structure we need to understand:
      - Action creators describe the state changes
      - Reducers execute the state changes
      - Connecting the Redux store to React components.
    
## Slices
- Top-Level `state properties`
- Features of an application
- Can be data values, array of object, or strings
  
          state = {                 --> THIS STATE HAS 2 SLICES
              todos:                            ---> SLICE 1
               [                                ---> ARRAY OF OBJECTS            
                    {                            
                      id: 0,
                      text: 'Complete the learn Redux course',
                      isCompleted = false
                    },
                ],
               visibility: 'SHOW_INCOMPLETE'     ---> SLICE 2
                                                 ---> STRING
            };

## COMPLEX STATES ACTIONS AND PAYLOADS
`**** Remember ****`
- Actions are JavaScript objects that have a type property and are dispatched to the store using the store.dispatch() method.
- Action change one slice at a time
- An actions type pattern is `sliceNae/actionDescriptor`
- 

