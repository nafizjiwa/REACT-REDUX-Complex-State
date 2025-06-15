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
#### (defines an Apps Inital State)
- Top-Level `state properties`      `Slice=StateProperty`
- Can be data values, array of object, or strings
  
        const initialState = {                 --> THIS STATE HAS 2 SLICES
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

***SWITCH CASES FOR ADDING TO AN ARRAY OR REMOVING FROM AN ARRAY</br>
- Only the state of the individual slice is needed to change that specific slice</br>



      case 'sliceName1/actionAdd':
            return [...state.sliceName1, action.payload];
      case 'sliceName1/actionRemove':
            return arraySliceName1.filter(element=>element.id !== action.payload.id);
     

## combineREDUCERS
- CombineReducer s creates the functions of the rootReducer</br>
  #### 1.CALLS ALL SLICE REDUCERS WITH THEIR SLICE OF STATE</br>
  #### 2.STORES THE RETURN STATE IN A NEW OBJECT</br>
  #### 3.NEW STATE IS RETURNED</br>
    
          const rootReducer = (state = {}, action) => {
             const nextState = {  2.
                todos: todosReducer(state.todos, action), 1.
             };   
             return nextState  3.
           };
           const store = createStore(rootReducer);
- CombineReducers, avoids the manual creation of the rootReducer function
- CombineReducer accepts the reducers object and returns a rootReducer function which contains each slice state and action

              //REDUCER OBJECT CONTAINS ALL SLICE REDUCERS UPDATING ALL SLICES OF THE STORE'S STATE
      const reducers = {      2.
          todos: todosReducer, 1.  //keyName = SliceName: SliceReducerName//
      };   
      const rootReducer = combineReducers(reducers);
      const store = createStore(rootReducer);
         //STORE OBJECT CREATED BY PASSING IN rootREDUCER
  
### When an Action is dispatched --> arrives at store --> rootReducer is executed which calls all slice reducers --> the action and slice of state is passed down appropriate slice

## FILE STRUCTURE FOR REDUX

- For each state put its action creators and slice reducer in its own sub file which can be imported into the store.js

            src/
            |-- index.js
            |-- app/
                |-- store.js
            |-- features/
                |-- featureA/
                    |-- featureASlice.js
                |-- featureB/
                    |-- featureBSlice.js

- So the in the store.js file:

      import { createStore, combineReducers } from 'redux';

      // FIRST, IMPORT SLICE REDUCER
      import { sliceReducerA } from '../featuresFile/sliceAReducer.js';
      import { sliceReducerB } from '../featuresFile/sliceBReducer.js';

      //SECOND, CONSTRUCT THE REDUCERS OBJECT
      const reducers = {
        // Add the slice properties here
         sliceA: sliceAReducer,
         sliceB: sliceBReducer   
      }

      //THIRD & FOURTH, PASS REDUCER OBJECT INTO COMBINEREDUCERS THEN CREATE STORE
      const store = createStore(combineReducers(reducers))

