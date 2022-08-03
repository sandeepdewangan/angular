
# ngRx

### Step 01: Install Package
`npm install @ngrx/store`

### Step 02: Create a store
`state/counter.state.ts`
```ts
export interface CounterState{
    counter:number;
}
export const initialState:CounterState = {
    counter: 0,
}
```
### Step 03: Create Actions
`state/counter.actions.ts`
```ts
import { createAction } from "@ngrx/store";

export const increment = createAction('increment');
export const decrement = createAction('decrement');
export const reset = createAction('reset');
```
### Step 04: Create Reducer
`state/counter.reducer.ts`
```ts
import { createReducer, on } from "@ngrx/store"
import { decrement, increment, reset } from "./counter.action";
import { initialState } from "./counter.state"

const _counterReducer = createReducer(
    initialState,
    on(increment, (state) => {
      return {
        ...state,
        counter: state.counter + 1,
      };
    }),
    on(decrement, (state) => {
      return {
        ...state,
        counter: state.counter - 1,
      };
    }),
    on(reset, (state) => {
      return {
        ...state,
        counter: 0,
      };
    })
  );
  
  export function counterReducer(state:any, action:any) {
    return _counterReducer(state, action);
  }
```

### Step 05: Import store into app module
`app.module.ts`
```ts
imports: [
    BrowserModule,
    ......
    StoreModule.forRoot({counter: counterReducer}), <-- This
  ],
```
### Step 06: Usage in Component by Injecting
```ts
 constructor(private store: Store<{counter: CounterState}>) {}
  // counter is the name which we specified in the app.module.ts
  // another counter is the name of our variable to increment which is of type number.
  
  onIncrement(){
   this.store.dispatch(increment()); // from actions
  }
  onDecrement(){
   this.store.dispatch(decrement());
    
  }
  onReset(){
   this.store.dispatch(reset());
  }
```
### Step 07: Outputing
```ts
export class CounterOutputComponent implements OnInit, OnDestroy {
  counter:number = 0;
  counterSubscription:Subscription = new Subscription();

  constructor(private store: Store<{counter: CounterState}>) { 
  }
  ngOnInit() {
    this.counterSubscription = this.store.select('counter').subscribe((data)=>{
      this.counter = data.counter;
    });    
  }
  ngOnDestroy(){
    if(this.counterSubscription){
      this.counterSubscription.unsubscribe();
    }
  }
}
```
```html
<h3>Counter: {{ counter }}</h3>
```

### Step 07 (Update): With Observable
```ts
export class CounterOutputComponent implements OnInit {
  counter$ : Observable<{counter:number}> = new Observable();
  constructor(private store: Store<{counter: CounterState}>) { 
  }
  ngOnInit() {
    this.counter$ = this.store.select('counter');
  }
}
```
```html
<h3>Counter: {{ (counter$ | async)?.counter }}</h3> 
```



## Custom Data Passing

### Step 01: Create Actions
`state/counter.actions.ts`
```ts
export const customIncrement = createAction('custom-increment', props<{value:number}>());
```
### Step 02: Create Reducer
`state/counter.reducer.ts`
```ts
on(customIncrement,(state, action)=>{
        return{
            ...state,
            counter: state.counter + action.value,
        }
    }
    ),
```

### Step 03:
```ts
 value:number = 0;

  constructor(private store:Store<{counter:CounterState}>) { }
  onAdd(){
    this.store.dispatch(customIncrement({value: this.value}));
  }
```