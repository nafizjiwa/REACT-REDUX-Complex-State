# REACT-REDUX-For-Complex-State

## Strategies for Complex State
- A REDUX store is ideal for managing Complex data state (State Management)</br>
- REDUX: one-way data flow --> State → View → Actions → State → View</br>
- WHEN designng a store’s state we need:</br>
      - Action creators describe how to change the state</br>
      - Reducers functions that modify state</br>
      - To connect Redux store to React components.</br>
    
## Slices 
#### (defines an Apps Inital State)
- A Redux slice is a property of the state object managed by Redux store. Slice=StateProperty`
- State objects can be data values, array of object, or strings
  
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
            JS objects with a properties of type and payload 
            Creators are dispatched to the store with store.dispatch() method.
- Action creators change one slice at a time
- Actions update state based on their type pattern =`sliceName/actionDescriptor`

              const actionName = (data) {
                    return {                  //ACTION OBJECT
                          type: 'sliceName/actionName',
                          payload: data
                    }
              }

- ACTIONS CREATORS CAN ALSO HAVE A:

      payload — additional data passed to the reducer to change the state
- For Example an App with a SearchBar componenr can have 2 actions which get dispached to change state of that searchbar whenever a user input in the SearchBar

*** 1. If the user enters a searchTerm of Spaghetti  &emsp;  &emsp; &emsp;   *** 2. If the user clears the search field no data sent but an empty string ' '

            1. store.dispatch({                                           2. store.dispatch({ 
              type: 'searchTerm/setSearchTerm',                                      type: 'searchTerm/clearSearchTerm' 
              payload: 'Spaghetti'                                                 });
            });                                                             // The resulting state: { ..., searchTerm: '' }
            // The resulting state: { ..., searchTerm: 'Spaghetti' }

## IMMUTABLE UPDATES 
#### (Reducers are Functions Which Decide How to Change the State)
- A Dispatched action calles a REDUCER:</br>
        1. The Reducers take in AN ACTION AND A CURRENT STATE --> the next state</br>
        2. Reducers make a copy of the state (don't mutate state)</br>
              For arrays and objects with mutable data use a spread operator(...)</br>

## REDUCER COMPOSITION
- For small appications a single reducer can manage every slice of the store's state
- For large application follow --> Use Reducer Composition
- Multiple slice reducers responsible for updating only one slice/part of the state
- Multiple Reducers results are recombined by a rootReducer --> A single state object.
  
|####|REDUCER COMPOSITION PROCESS FOR EACH SLICE REDUCER|
|-----|-----|
| ==> |Each called with incoming action and IT'S OWN slice of state|
| ==> |Then EACH REDUCER determines if they need an update or returns state unchanged|
| ==> |rootReducer calls all reducers and reassembles the updated slices|
           
  
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
  #### 2.STORES THE NEW STATE IN A NEW OBJECT</br>
  #### 3.RETURNS A NEW STATE</br>
    
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
- JOB OF PRESENTATIONAL COMPONENT IN REDUX:
            - Render the data of their SLICE OF STATE
            - Dispatch ACTION in response to user interaction 

- 1st, IMPORT feature components to App.js
  
            import ../feature/sliceNameFolder/sliceNameFeatureComponent.js
##### TO RENDER DATA AND DISPATCH ACTIONS
- 2nd, PASS PROPS (slice of state, dispatch method) from App to FeatureComponent.

            import FeatureComponent from './file-path';
            <App>
              <FeatureComponent               //PASSING PROPS
                stateName={sate.stateSliceName} 
                dispatch={dispatch_method_from_store}
              />
            </App>
            
- 3rd, EXTRACT PROPS: slice of state and dispatch method

              const MyComponent = ({state, dispatch} or props) => {
                        or const { state, dispatch } = props
                    // MyComponent code
                  }
##### ONCE FeatureComponent HAS ACCESS TO SLICE OF STATE THE COMPONENT DATA CAN BE RENDERED
- 4th, RENDER COMPONENT using data from the slice of state

            const featureComponent = (props) =>{
                    const { sliceOfState, dispatch } = props
                    return (  <div>
                                {render slideOfState data here}
                              </div>
                          )            
              }
##### DISPATCH AN ACTION
- 5th, IMPORT ACTION CREATORS from sliceReducer files

              import { sliceReducer } from './feature/sliceReducerFolder/sliceReducerFile.js';

- 6th, DISPATCH ACTION with the action creator in response to user inputs
      - When the dispatch method is passed to a component don't call store.dispatch only dispatch

            dispatch(actionCreator(payload));
|Review Notes|
|----|
|Action Creator are functions that create and return actions (an object).. the object has 2 props type & payload|
|Reducers determines how a state changes based on the action it receives|
|An action.type is the type action Eg.'Add a To Do'|
|An action.payload is the action the creator carries out. Eg. "Buy groceries"|
|store.dispatch(action) accepts an action and sends the action to the store's reducer|
|Spread syntax (...) and array methods (.map(), .slice(), .filter()) are used to immutably update the state of a complex app|
|The Reducer composition pattern manages a Redux store with multiple slices|
|root reducer delegates actions to slice reducers to update their own state in the store then reassembles the slices into a new state object|
|combineReducers() method accepts all reducer functions and returns a rootReducer|
|Redux Slice reducers handles actions and updates the data for a given slice|
|The store is passed from (index.js) through the main <App /> component as a prop|
|The <App /> component passes the slices of the store’s state to its sub-components|
|“prop drilling” or “prop threading” - props are “threaded” through the top-level component -> presentational components.|
|Redux Toolkit helps avoid “prop threading” |
|store.subscribe(listener_function) accepts a listener|
|When an action is dispatched to the store all subscribed listeners are executed|
|feature-based pattern - Organize code so each feature (create a todo, cart) has its own folder not organized by types (actions, or components)|




 
