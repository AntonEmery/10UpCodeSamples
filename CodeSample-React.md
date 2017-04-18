
## Code Samples for Front End Engineer Position
Anton Emery

##### React
The challenge I faced in this problem was passing data from an API to a child route, which would then display a list of social media posts. The entire repo can be found here. https://github.com/AntonEmery/alaska-project

Lets take a look at the Router
##### The Router
```
<Route path='/' component={Main}>
      <IndexRoute component={IntroPage} />
      <Route path="PostsContainer" component={PostsContainer} />
      <Route path="OutroPage" component={OutroPage} />
</Route>
```
The `Main` component loads, which automatically loads the `IntroPage` since it is the IndexRoute component. Once the API call is finished the user is automatically directed to the `PostsContainer`route which displays the API data. 

The problem i worked to solve was how to pass the data from the API call in `Main` to the IndexRoute in `Intro Page`, and then to the different `PostsContainer` route. Below is a short abbreviated example of the components. 
##### Main Component

```
var Main = React.createClass({
  getInitialState: function() {
    return {
      posts: [],
      loading: true
    };
  },
```
We are setting the initial state, defining `posts` as an empty array and setting `loading` to `true`
```
  componentDidMount: function() {
    var that = this;
    jsonp('https://www.apiurl.com', null, function (err, data) {
    if (err) {
      console.error(err.message);
    } else {
        var dataArray = [];
        for(i=0; i<data.length; i++) {
        dataArray.push(data[i]);
      }
      that.setState({loading: false, posts: dataArray});
    }
  })
},
```
Once the component mounts we make the API call, adding the results to an array. We then call `setState`, setting `loading: false`, and `posts` to the array results of our API call.

Calling `setState` triggers a rerender of our component. Since `this.state.loading`is now false the Loading text is no longer displayed in the Index Route of `Intro Page` can be rendered
  ```
  render: function() {
    return (
      <div>
        <div className="container card-area wrapper">
          <div className="row row-centered">
           {this.state.loading ?
             <p id="loading">Loading...</p> :
             React.cloneElement(this.props.children, { apiData: this.state.posts, status: this.state.loading})}
          </div>
        </div>
      </div>
    );
  }
});
```
##### Intro Page Component
The Intro page component displays a slogan then after five seconds redirects the user to the `PostsContainer` route, which displays the various social media posts
```
var IntroPage = React.createClass({
  contextTypes: {
    router: React.PropTypes.object.isRequired
  },
  goToPosts: function() {
    var that = this;
    setTimeout(function() {
      that.context.router.push({
        pathname: '/PostsContainer'
      })
    }, 5000)
  },
  componentDidMount: function() {
    this.goToPosts();
  },
  render: function() {
    return (
      <div className="col-md-12 col-sm-12 col-xs-12">
      <p id="intro-text">This is why #iflyalaska</p>
      </div>
    );
  }
});
```
All the components have the data from the API call made in `Main Component` as part of their props, which allows me to render it to the page in the `Posts Container` component.

The magic that lets this happen is the `React.cloneElement` method. This creates a copy of the current element, passing in an object with the key of `apiData` whose value is the result of the api call. This lets me access it on all the child elements using `this.props.apiData`
