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
- Top-Level `state properties`      `Slice=StateProperty`
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

## ACTIONS AND PAYLOADS of COMPLEX STATE
`**** Remember ****`
            Actions are JavaScript objects that have a type property and are dispatched to the store using the store.dispatch() method.
- Action change one slice at a time
- To clarify which state an actions updates its type pattern =`sliceName/actionDescriptor`

- ACTIONS CAN ALSO HAVE A:

      payload — data passed to the reducer in order to carry out the desired change-of-state
- For Example an App with a SearchBar would have 2 actions which get dispached to change state when a user input in the SearchBar

*** 1. If the user enters a searchTerm of Spaghetti  &emsp;  &emsp; &emsp;   *** 2. If the user clears the search field no data sent but an empty string ' '

            1. store.dispatch({                                           2. store.dispatch({ 
              type: 'searchTerm/setSearchTerm',                                      type: 'searchTerm/clearSearchTerm' 
              payload: 'Spaghetti'                                                 });
            });                                                             // The resulting state: { ..., searchTerm: '' }
            // The resulting state: { ..., searchTerm: 'Spaghetti' }



 




