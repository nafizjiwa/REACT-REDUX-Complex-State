# REDUX API-Strategy-For-A-Complex-State

## Intro Strategies for Complex State
- REDUX - porvides a centralized STOREto MANAGE STATE</br>
        - is one-way data flow --> State → View → Actions → State → View</br>
- DESIGN a store’s state we need:</br>
      - Action creators - To describe how to change the state</br>
      - Reducers functions - To modify the state</br>
      - Connection - Connect the Redux store to React components.</br>
    
## Slices 
#### (1st Define the store's InitialState consider the # of Slices or features of that state )
- A Redux: `Slice = THE TOP LEVEL StateProperty` OF THE STATE OBJECT
- Slice's values: data, array of object, or strings
  
        const initialState = {         --> THIS STATE OBJECT HAS 2 SLICES
              todos:                            ---> SLICE 1
               [                                ---> ARRAY OF OBJECTS         
                    { id: 0,                         
                      text: 'Complete the learn Redux course',
                      isCompleted = false  },
                ],
               visibility: 'SHOW_INCOMPLETE'     ---> SLICE 2 `STRING`
            };

## ACTIONS AND PAYLOADS of COMPLEX STATE 
#### (2nd The State's Slices are known now define how to trigger changes to these slices of state with actions)
- Actions are dipatched to the store with `store.dispatch(ACTIONobject)`</br>
- Complex state action object:
### ACTION OBJECT
        { 
            type:'sliceName/actionName,
            payload: data
         }  
- The naming clarifies the slice of state to be updated.</br>

        const theActionISaJSobject(actionDescriptor) = {
             type: 'sliceName/actionDescriptor', --> Action types Naming pattern
             payload: 'data= variable, number, string'
          }
  

### Once the type of actions are determined create action creators for those actions
`**** Actions creators ****`
            -Functions that return action objects everytime they are called 
            -Dispatched to the store with `store.dispatch(action object or action creator)`.

             const actionDescriptorName = (data) {     //Action CREATOR
                   return {                  //returns ACTION OBJECT
                         type: 'sliceName/actionDescriptorName',  --> Naming Pattern
                         payload: data   --> its value changes the state
                   }
             }
##### Example An App with a SearchBar component
-  It has 2 types of actions to change the searchbars `State`. The `State` change is based on what the user inputs into the SearchBar

*** 1. If the user enters `Spaghetti`  &emsp;  &emsp; &emsp; &emsp;  &emsp; &emsp; &emsp;  &emsp; &emsp;  *** 2. If the user enters an empty string ' '

            1. store.dispatch({                                           2. store.dispatch({ 
              type: 'searchTerm/setSearchTerm',                                      type: 'searchTerm/clearSearchTerm' 
              payload: 'Spaghetti'                                                 });
            });                                                             // The resulting state: { ..., searchTerm: '' }
            // The resulting state: { ..., searchTerm: 'Spaghetti' }

## IMMUTABLE UPDATES 
#### (3rd Once how the changes to State are defined with actions, create a Reducer to Execute Changes to the State)
- A store's reducer is called when an action is dipatched
- REDUCER:</br>
        1. Takes in an `ACTION` AND A `CURRENT STATE` --> returns `the next state`</br>
        2. Reducers make a copy of the state (don't mutate state) with a spread operator(...)</br>
              - To make a copy of state(...state,)</br>
        3. In the reducer's switch build cases for all defined action types-'sliceName/actionName'

          const sliceNameReducer = (state = initialState, action) => {
                  switch (action.type) {
                    case 'sliceName/actionName':
                      return {  ...state,
                        sliceName: sliceNames new value = action.payload
              };
               

## REDUCER COMPOSITION
- For small appications a single reducer can manage all the store's state
- For large application follow:
          --> `Reducer Composition pattern`
- We create individual `Slice Reducers` to update only `one slice` of the `state`
- The results of all `Slice Reducers` are recombined wtih a `rootReducer` --> Into a single state object.
  
||HOW THE REDUCER COMPOSITION PATTERN WORKS|
|-----|-----|
|1st | Action is dipatched to the store|
|2nd |rootReducer calls each slice reducer|
||==> The call passes the action and IT'S OWN slice of state to each `Slice Reducer`|
| 3rd |EACH REDUCER determines if they need an update or returns state unchanged|
| 4th |The rootReducer reassembles the updated slices in a new `state object`|
           
  
     *********************************************************
     const rootReducer = (state = {}, action) => {
         const nextState = {                                   //ALL SLICE REDUCERS CALLED
               sliceA: sliceAReducer(state.sliceA, action),    //SLICES RECEIVE THEIR
               sliceB: sliceBReducer(state.sliceB, action)     //OWN SLICE OF STATE VALUES
             }
            return nextState;        //REDUCER RETURNS NEXT STATE
     }
     **********************************************************

#### ***Switch cases for ADDING OR REMOVING FROM AN ARRAY</br>
- Only individual slice's STATE is needed to change that individual slice</br>
- Returned slice's state match the case's slice name

      case 'sliceName1/actionAdd':
            return [...state.sliceName1, action.payload];
      case 'sliceName1/actionRemove':
            return arraySliceName1.filter(element=>element.id !== action.payload.id);
     

## combineREDUCERS
- CombineReducers creates the functions of the rootReducer</br>
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
|***Store Set Up Requires***|
|Importing functions createStore(), combineReducers() and rootReducer
|Importing slice reducers from their feature folders feature/featureSliceName.js name|
|Then Combine reducers object with combineReducers({...reducersObject})|
|CombineReducer returns a rootReducer function with each slice state and action|
|Reducer object contains all slices and their reducer|
|Next, create a store using combineReducers (contains all the slice of states)|
|export const store = createStore(combineReducers({
  sliceA: sliceAReducer, sliceB: sliceBReducer,}));|
|***Connect Redux Store to React Component App***|
|To pass store from (index.js) to main <App /> component as a prop|
|import { store } from './app/store.js'|
|***Pass data down to presentational component***|
|Pass store to Presentational Components|
|What is Passed? Current State and dispatch Method|
|\<App currentState={store.getState} dispatch={store.dispatch}/>|
|***When States change the component needs to re-render with most up-dated data***|
|Subscribe render or render function to changes to the state of the store|
|store.subscribe(listener_function) accepts a listener|
|store.subscribe(render) listens when state has changed and updates its data|
|App.js renders all slices of state and their dispatch methods as above: Eg.|
|-------------------------------------------------------------------------------------------------------------------------------------------------------|
 
            Import slice Component thenRender Component
            const App = (props) => {
                  const { state, dispatch } = props; 
                         return (
                               <RenderedSliceNameComponent      
                                    sliceName={state.sliceName}
                                    otherSlice= {state.otherSlice}
                                    dispatch={dispatch}
                                    />
                        )
            }
|Review Notes|
|----|
|store.dispatch(action) - sends the action to the store's reducer|
|Action Creators are functions that return actions objects with 2 props type & payload|
|Reducers receives an action and changes state|
|Action.type is the type action Eg.'Add a To Do'|
|Action.payload holds data used by action creator to carry out action. Eg. "Buy groceries"|
|Spread syntax (...) and array methods (.map(), .slice(), .filter()) immutably update the state|
|The Reducer composition pattern manages a Redux store with multiple slices|
|Root Reducer delegates actions to slice reducers to update their own state in the store then reassembles the slices into a new state object|
|combineReducers() method accepts all reducer functions and returns a rootReducer|
|Redux Slice reducers handle actions and updates the data for a given slice|
|The store is passed from index.js throught to <App/> compnent|
|The App component passes the slices of the store’s state to its sub-components|
|“prop drilling” or “prop threading” - props are “threaded” through the top-level component -> presentational components|
|“prop threading” is not ideal so use Redux Toolkit to avoid "prop threading"|
|When an action is dispatched to the store all subscribed listeners are executed|
|feature-based pattern - Organize code so each feature (create a todo, cart) has its own folder|





 
