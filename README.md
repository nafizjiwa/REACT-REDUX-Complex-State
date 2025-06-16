# REACT-REDUX-For-Complex-State

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

- So in the store.js file:

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
  
## PASSING STORE DATA THROUGH TOP LEVEL REACT COMPONENT
- React Components and their Slice file's should be kept in the same folder.</br>
      - React Component renders the data</br>
      - Slice files manages the data</br>
- React applications, top-level components, like App.js,</br>
            1. Render Components and </br>
            2. Pass data down to Components as props.</br>
- Redux Applications, the data is passed to feature Component as:</br>
            1.The Slices of store states to render
              - state.sliceName can be passed to <SliceNameComponent /></br>
            2.The store.dispatch method to trigger state changes with actions
              - <Component /> will dispatch setComponent() actions.</br>

  #### TO PASS CURRENT STATE AND ITS DISPATCH TO <App />: </br>
  1. Import store to the index.js file
      - ./app/store.js
      - ./ means 'starting from the directory of this file...' where "this file" is index.js.
  2. Pass the store's state to <App /> as a prop state
      - Get current state of store --> store.getState()
      - Render <App /> passing state as a prop
      - <App state={get current state of store } />
  3. For App to render App needs a dispatch method
      - Pass store.dispatch to <App /> as prop dispatch
            <App dispatch={store.dispatch} /> not store.dispatch()
  4. Subscribe the render function to changes to the store so the whole <App /> component can re-renders whenever changes in the store occcur.
       - To subsribe a method to changes to the store: store.subscribe(FunctionName)
       - --> store.subscribe(renderfunction).

## USING STORE DATA WITHIN FEATURE COMPONENTS
- State and store.dispatch are passed to <App /> then App passes dispatch method and slices of store's state to feature components `AS PROPS`.

- 1st, Import feature components to App.js
  
            import ../feature/sliceName/sliceNameFeatureComponent.js
- 2nd, Render each feature component by passing the slice of state and dispatch method as props.

            impo
            <section>
              <h2>All Recipes</h2>
              <AllRecipes
                allRecipes={visibleAllRecipes} 
                dispatch={dispatch}
              />
            </section>
            
              
                  

- 3rd, In each feature Component
              - extract state and dispatch props
              - Render component using date from the slice of state
              - Import action creators from slice files
              - Dispatch action in response to user inputs

The action.payload property holds data for the reducer to caryy ou an action.</br>
The spread syntax (...) and array methods (.map(), .slice(), .filter()) are used to immutably update the state of a complex app.</br>
Reducer composition is pattern for managing a Redux store with multiple slices.</br>
The root reducer delegates actions to slice reducers to update their own state in the store then reassembles the slices into a new state object.</br>
combineReducers() is a method accepts a collection of reducer functions and returns a rootReducer that implements the reducer composition pattern.</br>
Slice reducers are often written in separate files in Redux known as Redux Ducks.</br>
The store is passed from (index.js) through the main <App /> component as a prop. </br>
The <App /> component passes the slices of the store’s state to its sub-components.
“prop drilling” or “prop threading” because they are “threaded” through the top-level component -> presentational components. This isn’t ideal, considering that the top-level component doesn’t make use of those props.</br>
Redux Toolkit to avoid “prop threading” </br>



 
