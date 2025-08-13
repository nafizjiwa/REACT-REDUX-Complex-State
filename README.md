# REDUX API-Strategy-For-A-Complex-State

## Intro Strategies for Complex State
- REDUX - porvides a centralized STOREto MANAGE STATE</br>
        - is one-way data flow --> State → View → Actions → State → View</br>
- DESIGN a store’s state we need:</br>
      - Action creators - To describe how to change the state</br>
      - Reducers functions - To modify the state</br>
      - Connection - Connect the Redux store to React components.</br>
    
## SLICES 
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
#### Action Object
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
#### (3rd Once how the changes to State are defined with actions, create a REDUCERS to Execute Changes to the State)
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
               

## REDUCER COMPOSITION - MULTIPLE SLICE REDUCERS
- For small appications a single reducer can manage all the store's state
- For large application follow:
          --> `Reducer Composition pattern`
- We create individual `Slice Reducers` to update only `one slice` of the `state`
- All `Slice Reducers` are recombined with a `rootReducer` --> Into a single state object.
  
||HOW THE REDUCER COMPOSITION PATTERN WORKS|
|-----|-----|
|1st | Action is dipatched to the store|
|2nd |rootReducer calls each slice reducer|
||==> The call passes the action and IT'S OWN slice of state to each `Slice Reducer`|
| 3rd |EACH REDUCER determines if they need an update or returns state unchanged|
| 4th |The rootReducer reassembles Slices in a --> `new STATE OBJECT`|
           
  
     *********************************************************
     import { createStore } from 'redux';
     
      *******Create Slices**
     const initialStateSlice(A) = []
     const slice(A)Reducer = (initialState, action) => {switch cases for this slice}
     
       *******Combine Slices into rootReducer**
     const rootReducer = (state = {}, action) => {
         const nextState = {                                   //ALL SLICE REDUCERS CALLED
               sliceA: sliceAReducer(state.sliceA, action),    //SLICES RECEIVE THEIR
               sliceB: sliceBReducer(state.sliceB, action)     //OWN SLICE OF STATE VALUES
             }
            return nextState;        //REDUCER RETURNS NEXT STATE
     }
     
        *******Create store with rootReducer**
     const store = createStore(rootReducer)
     **********************************************************

#### ***Switch cases for ADDING OR REMOVING FROM AN ARRAY</br>
- Only individual slice's STATE is needed to change that individual slice</br>
- Returned slice's state match the case's slice name

      case 'sliceName1/actionAdd':
            return [...state.sliceName1, action.payload];
      case 'sliceName1/actionRemove':
            return arraySliceName1.filter(element=>element.id !== action.payload.id);
     

## combineReducers() FUNCTION ENABLES THE REDUCER COMPOSITION by RETURNING A rootReducer( )

##### CombineReducers(accepts a reducer object)
##### --> Returns an auto generated Root Reducer contain all the slices
##### --> SO NO NEED TO CODE FOR A REDUCER
##### --> Previous hard coded rootReducer:
    
      const rootReducer = (state = {}, action) => {
         const nextState = {  
            slice(1): slice(1)Reducer(state.slice(1), action), 
                 };   
           return nextState  
       };
           const store = createStore(rootReducer);
#### INSTEAD NOW USE the combineReducer to Generate a rootReducer
#### CREATE a Reducer Object -- Contain all Slice Reducers of the store STATE

      const reducers = {      
          slice(1): slice(1)ReducerName, 
          //keyName = SliceName: value = sliceReducerName//
      };   
#### INPUT Object into combineReducer(Object) --> returns a root reducer as above

      const rootReducer = combineReducers(reducers);
#### Then create a STORE object with the rootReducer

      const store = createStore(rootReducer);
#### ALL THIS CAN BE CONDENSED INTO:

      import { createStore, combineReducer } from 'redux';
      const store = createStore(combineReducers({
                    slice(1): slice(1)Reducer,
                    slice(2): slice(2)Reducer
                }));

## FILE STRUCTURE FOR REDUX
- For each state put its action creators and slice reducer in its own sub file which can be imported into the store.js

            src/
            |-- index.js
            |-- app/
                |-- store.js
            |-- features/
                |-- feature(1)Folder/
                    |-- feature(1)Slice.js
               ...etc

- The `store.js` file contains the imported slices, `rootreducer`, and `store`:

      import { createStore, combineReducers } from 'redux';

      // FIRST, IMPORT SLICE REDUCER
      import { sliceReducer(1) } from '../featuresFile/slice(1)Reducer.js';
      import { sliceReducer(2) } from '../featuresFile/slice(2)Reducer.js';

      //SECOND, CONSTRUCT THE REDUCERS OBJECT
      const reducers = {
        // Add the slice properties here
         slice(1): slice(1)Reducer,
         slice(2): slice(2)Reducer,  
      }

      //THIRD & FOURTH, PASS REDUCER OBJECT INTO COMBINEREDUCERS THEN CREATE STORE
      const store = createStore(combineReducers(reducers))
  
## PASSING STORE DATA THROUGH TOP LEVEL REACT COMPONENT
- React Components and their Slice file's are kept in the same folder.</br>
- DATA FLOW -- STORE --> App compnent --> Feature component

        |-- features/
                |-- feature(1)Folder/
                    |-- feature(1)ReactComponent.js --- Renders Data
                    |-- feature(1)Slice.js   --- Manages Data
- In Redux, THE DATA passeD to feature Components via App.js as props are:</br>
            1.The Slices of state (state.sliceName) passed to component to render</br>
              - `<SliceNameComponent state.sliceName />`</br>
            2.The store.dispatch (uses actions to trigger state change)</br>
              - `<Component />` will dispatch setComponent() actions.</br>

#### TO PASS STORES CURRENT STATE AND ITS DISPATCH TO <App />: </br>
##### 1. In index.js file import the store 
- ./ means 'starting from the directory of this file...' "this file" = index.js.</br>

        import store from './store.js'     
##### 2. Pass the store's current state to <App /> as a prop = state
- To get current state --> store.getState()
        
        <App state={get current state of store } />
##### 3. Pass the store.dispatch method to <App /> as a prop = dispatch
- Don't pass function call store.dispatch().
 
        <App dispatch={store.dispatch} /> -- NOT store.dispatch()
##### 4. Subscribe Apps render() function to the store so store changes cause <App /> to re-render
- To subsribe a method use: store.subscribe(FunctionName)

         const render = () => {
             root.render(
                 <App  state={store.getState()}
                       dispatch={store.dispatch}
                  />, )
               }
             render();
          store.subscribe(render).
  
## USING STORE DATA WITHIN FEATURE COMPONENTS
- Data from Stores state and store.dispatch are passed to <App /> then passed to feature components `AS PROPS`.
- FEATURE COMPONENTS:
            - Render the data of their SLICE OF STATE
            - Dispatch ACTION in response to user interaction 

##### TO RENDER DATA AND DISPATCH ACTIONS
- 1st, IMPORT feature components to App.js
- 2nd, PASS PROPS (slice of state, dispatch method) from App to FeatureComponent.

        -App.js-file-
            import FeatureComponent from '../feature/sliceFolder/sliceFeatureComponent.js';
            export function App(props) {
              const {state, dispatch} = props;
              return (
                      <FeatureComponent               //PASSING PROPS
                        stateName={state.stateSliceName} 
                        dispatch={dispatch_method}
                      /> )
            
- 3rd, EXTRACT PROPS in Feature Component: slice of state and dispatch method

              const FeatureComponent = ({state, dispatch} or props) => {
                        or const { sliceOfState, dispatch } = props
                    // MyComponent code
                  }
##### NOW Feature Component HAS ACCESS TO SLICE OF STATE THE DATA CAN BE RENDERED
- 4th, RENDER COMPONENT using data from the slice of state

            const featureComponent = (props) =>{
                    const { sliceOfState, dispatch } = props
                    return (  <div>
                                {render JSX slideOfState data here}
                              </div>
                          )            
              }
##### FEATURE COMPONENT WANTS TO DISPATCH AN ACTION
- 5th, IMPORT ACTION CREATORS from sliceReducer files to DISPATCH ACTION

              import { ACTION CREATOR } from './feature/sliceFolder/sliceReducerFile.js';

- 6th, DISPATCH ACTION with the action creator in response to user inputs
      - Dispatch is passed to component don't call store.dispatch only dispatch

            dispatch(actionCreator(payload));
|Review Notes|
|----|
|***To Build a React-Redux Application with multiple slices of state***|
|store.dispatch(action) - dispatches the action to the store's reducer|
|Action Creators are functions that return actions objects with 2 props type & payload|
|Reducers receives an action and changes state|
|Action.type is the type action need changed Eg.'Add a To Do'|
|Action.payload holds data eeded by Reducer to complete action. Eg. "Buy groceries"|
|Methods to immutably update state -- .map( ), .slice( ), .filter( )|
|Reducer Composition is a pattern to manage a Redux store with multiple slcies|
|rootReducer delegates actions to its slice reducers|
|SliceReducers update their slice's of the store state|
|rootReducer reassembles slice into new object|
|Redux library's combineReducers() accepts reducers functions and returns a root reducer|
|'prop drilling' or 'prop threading'|
|props are threaded from top-level component--> to presentational component|
|where the store is passed from the entry file index.js|
|to <App stateProp='store passed as prop' /> component|
|<App /> component then passes the slices of the store's stae to sub components|
|prop drilling not ideal the Redux toolkit helps avoid drilling |
|----|
|***To Set Up a Store Requires***|
|Import the functions createStore(), combineReducers() and rootReducer|
|Importing slice reducers from their feature folders feature/featureSliceName.js name|
|Create a reducers object of all slice reducers using combineReducers({...reducersObject})|
|CombineReducer returns a rootReducer function with each slice state and action|
|Next, create a store of combineReducers(slices of the state)|
|export the store as const store = |
|= createStore(combineReducers({ slice(1): slice(1)Reducer, slice(2): slice(2)Reducer,}));|
|***Connect Redux Store to React Component App***|
|Pass the store from (index.js) to main <App state={store.getState()}/> component as a prop|
|import { store } from './app/store.js' to index.js|
|***Pass data down to presentational component***|
|\<App currentState={store.getState} dispatch={store.dispatch}/> --> then to components|
|***When States change the component needs to re-render with most up-dated data***|
|Subscribe render or render function to changes to the state of the store|
|store.subscribe(listener_function) accepts a listener|
|store.subscribe(render) listens when state has changed and updates its data|
