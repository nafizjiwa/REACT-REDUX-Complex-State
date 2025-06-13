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
#### (define an Apps Inital State)
- Top-Level `state properties`      `Slice=StateProperty`
- Features of an application
- Can be data values, array of object, or strings
  
          initialState = {                 --> THIS STATE HAS 2 SLICES
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

## ACTION CREATORS AND PAYLOADS of COMPLEX STATE 
#### (define how to change the state)
`**** Actions creators ****`
            JS objects with a type property 
            Creators are dispatched to the store with store.dispatch() method.
- Action creators change one slice at a time
- To clarify which state an actions updates its type pattern =`sliceName/actionDescriptor`

              const actionName = (data) {
                    return {                  //ACTION OBJECT
                          type: 'sliceName/actionName',
                          payload: data
                    }
              }

- ACTIONS CREATORS CAN ALSO HAVE A:

      payload — data passed to the reducer in order to change-of-state
- For Example an App with a SearchBar can have 2 actions which get dispached to change state whenever a user input in the SearchBar

*** 1. If the user enters a searchTerm of Spaghetti  &emsp;  &emsp; &emsp;   *** 2. If the user clears the search field no data sent but an empty string ' '

            1. store.dispatch({                                           2. store.dispatch({ 
              type: 'searchTerm/setSearchTerm',                                      type: 'searchTerm/clearSearchTerm' 
              payload: 'Spaghetti'                                                 });
            });                                                             // The resulting state: { ..., searchTerm: '' }
            // The resulting state: { ..., searchTerm: 'Spaghetti' }

## IMMUTABLE UPDATES 
#### (Use a Reducer to Change the State)
- A Store's reducer is called each time an action is dispatched
- Rules of a reducer:
        1. Reducers take in 1.an action 2.a current state and return the next state
        2. Reducers don't mutate the state they make a copy of the state
              For arrays and objects with mutable data use a spread operator(...)

## REDUCER COMPOSITION
- For small appications a single reducer can manage every slice of the store's state
- For large application follow --> Reducer Composition
- Individual slice reducers are responsible for updating only one slice of the state
- Result are recombined by a rootReducer --> form a single state object.
  
|####|REDUCER COMPOSITION PROCESS FOR EACH SLICE REDUCER|
|-----|-----|
| ==> |Each called with incoming action and slice of state|
| ==> |Only receives its slice of state|
| ==> |Then determines if they need an update or return state unchanged|
| ==> |rootReducer reassembles the updated slices by calling all reducers within itself|
           
  
     *********************************************************
     const rootReducer = (state = {}, action) => {
         const nextState = {                                   //ALL SLICE REDUCERS CALLED
               sliceA: sliceAReducer(state.sliceA, action),    //SLICES RECEIVE THEIR
               sliceB: sliceBReducer(state.sliceB, action)     //OWN SLICE OF STATE VALUES
             }
            return nextState;  //REDUCER RETURNS NEXT STATE
     }
     **********************************************************

***SWITCH CASES FOR ADDING TO AN ARRAY OR REMOVING FROM AN ARRAY
- Only the state of the individual slice is needed to change that specific slice

            case 'sliceName1/actionAdd':
                    return [...state.sliceName1, action.payload];
            case 'sliceName1/actionRemove':
                    return arraySliceName1.filter(element=>element.id !== action.payload.id);



