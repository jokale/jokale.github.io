---
layout: post
title:      "React/Redux Flow Joanna style "
date:       2020-06-02 14:18:30 -0400
permalink:  redux_flow_joanna_style
---


In this blog post I will be explaining how I am rendering my list of threads on my React/Redux application from explaining the very start of the process, to accessing the data, to storing that data, and finally how I am getting access that data once it's stored.


To start the process of accessing the data to render my list of threads on the page we start in my Threadds Container(thread is spelt threadds due to not being able to name it thread) as opposed to the specific Threaddlist component

```
import React from 'react'
import {fetchThreadds} from '../actions/fetchThreadds'
import Threaddform from '../components/Threaddform'
import Threaddlist from '../components/Threaddlist'
import Threaddshow from '../components/Threaddshow'
import {connect} from 'react-redux';
import {Route, Switch} from 'react-router-dom'

class ThreaddsContainer extends React.Component {
    
    componentDidMount(){
        this.props.fetchThreadds()
    }

    render(){
        console.log("render")
        return(

            <div>
                <Switch>
            <Route path='/thread/new' component={Threaddform}/>
 
             <Route path='/threads/:id' render={(routerProps) => <Threaddshow {...routerProps} threadds={this.props.threadds}/>}/>

            <Route exact path='/threads'render={()=> <Threaddlist threadds={this.props.threadds}/>} />
        
            </Switch>
            </div>
        )
    }
}

const mapStateToProps = state => {
 
    return {
        threadds: state.threadds
    }
}

export default connect(mapStateToProps, {fetchThreadds})(ThreaddsContainer);

```

We access the data from our store with passing mapStateToProps as the first argument into our connect() function. This just allows us to have access to grab state from the store. But for that to happen we must create a mapStateToProps function which is allowing us to grab the specific state from our store that allows us to pass our component's props. This function is returning the data we need which in this case is the threadds.

We then render the specific global state in our specific components as props

```
<Threaddlist threadds={this.props.threadds}/> 
```

Then our component mounts which means that the HTML within my container and the HTML within my Threaddlist component has been rendered onto the DOM. The can be accessed if necessary and also allows to manipulate the DOM and fetch any data needed. 


```
    componentDidMount(){
        this.props.fetchThreadds()
    }
		
		```
        
        

Whereby this.props is giving us access to global state and fetch threads is the action creator allowing us to fetch all the threads from our backend( where I am storing all the data)


```
\\fetchThreadds.js 

export function fetchThreadds() {
{

 
    return (dispatch) => {
        fetch('http://localhost:3000/threadds')
        .then(res => res.json())
        .then(jsonData => dispatch({
             type: 'FETCH_THREADDS',
             payload: jsonData

        }))
    }
    
}
```


Whereby a function is returned (due to Redux Thunk) from our API call with a payload of JSON data holding all the threads. Due to Redux Thunk and our connect function in which we are passing in 
fetchhThreadd  as a second arguement. 

From there, we can dispatch that function and action to our reducer below. 

```switch(action.type) {
    case 'FETCH_THREADDS':
       return {threadds: action.payload} ```
			 

Our reducer matches our type which is ``` 'FETCH_THREADDS' ``` with the relevant case statement which is also ```'FETCH_THREADDS' ``` and our reducers allows us to return our payload of data and allow that data to be saved in the store as threads. 

The process repeats because the first time around when we were trying to get access to the store there was nothing in our store this was just allowing us to create a foundation so when we have access to the data it renders pretty quickly. 

So again we able to get a specific state from our store and allow it to pass the props to the relevant components.

```
<Threaddlist threadds={this.props.threadds}/> 
```

Now that the Threaddlist component has access to props and now we can map the all the threads in the store.


```
\\ThreaddList.js 

    return(
        <div> 
            <Clicker/>
            {props.threadds.map(threadd => 
            
            <div key={threadd.id}> <Button size='lg' variant="outline-dark"><Link to={`/threads/${threadd.id}`}>{threadd.thread_title}<br></br></Link></Button>
            </div>
            )}
                        
    )
}


```

And return the threads onto the page to display all threads onto the page!

