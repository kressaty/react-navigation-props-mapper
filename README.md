## react-navigation-props-mapper

`yarn add react-navigation-props-mapper`

or

`npm i react-navigation-props-mapper --save`


## The problem
You're using react-navigation to navigate around you React Native app. The [documentation](https://reactnavigation.org/docs/intro/#Passing-params) describes you should use `this.props.navigation.state.params` to access props passed to your screen. For example:

```
render() {
  // The screen's current route is passed in to `props.navigation.state`:
  const { params } = this.props.navigation.state;
  return (
    <View>
      <Text>Chat with {params.user.userName}</Text>
    </View>
  );
}
```

You don't like this because you

1 . don't want your code to be tightly coupled to react-navigation

2 . want to reuse your component somewhere else

3 . don't find this very elegant

More in related [github issue](https://github.com/react-community/react-navigation/issues/935)

## The solution
This small package offers two functions to ease working with the props from react-navigation.
 
### `withMappedNavigationProps`
Use this function to be able to access the props passed to your screen *directly*. Eg. instead of `this.props.navigation.state.params.user.userName` you'd write `this.props.user.userName`. The function wraps the provided component in a HOC and passes everything from `props.navigation.state.params` as well as `props.screenProps` to the wrapped component.

#### Usage
When defining the screens for your navigator, wrap the screen component with the function. For example:

```
import { withMappedNavigationProps } from 'react-navigation-props-mapper'
...
const MainNavigator = StackNavigator({
  firstScreen:  { screen: withMappedNavigationProps(FirstScreenComponent) },
  secondScreen: { screen: withMappedNavigationProps(SecondScreenComponent) },
});
```

### `withMappedNavigationAndConfigProps`
When using a function in `static navigationOptions` to configure eg. a screen header dynamically based on the props, you're dealing with the same issues as mentioned above. `withMappedNavigationAndConfigProps` does the same as `withMappedNavigationProps` but also saves you some hassle when defining screen's `static navigationOptions` property. For example, it allows turning

```
static navigationOptions = ({ navigation }) => ({
  title: `${navigation.state.params.name}'s Profile!`,
  headerRight: (
      <HeaderButton title="Sort" onPress={() => navigation.navigate('DrawerOpen')} />
    ),
});
```
into
```
static navigationOptions = (props) => ({
  title: props.name,
  headerRight: (
      <HeaderButton title="Sort" onPress={() => props.navigation.navigate('DrawerOpen')} />
    ),
});
```
or
```
static navigationOptions = ({navigation, name }) => ({
  title: name,
  headerRight: (
      <HeaderButton title="Sort" onPress={() => navigation.navigate('DrawerOpen')} />
    ),
});
```


#### Usage
`import { withMappedNavigationAndConfigProps } from 'react-navigation-props-mapper` and use the same way as `withMappedNavigationProps`. In your screen component, use `static navigationOptions`, same as you'd do normally.


### Injecting Additional Props
Both `withMappedNavigationAndConfigProps` and `withMappedNavigationProps` accept an optional second parameter, of type `ReactClass` that gets all the navigation props and the wrapped component as a props. You may do some additional logic in this object. TODO example.


<!-- In many cases you may want to inject some additional props to your component. For example: when trying to do `this.props.navigation.navigate('SomeScreen', {someComplicatedObject: someComplicatedObjectInstance})`, `react-navigation` is likely to throw (if the object cannot be serialized to JSON), because it is recommended to pass only string props between screens, so that deep linking works.

You may thus pass the string and then use it in your component to get an object that is described by the string. Example: you pass `{username: 'John'}` and then in your component you do something like `userStore.getUserByUname(this.props.username)` to get the `User` object. Wouldn't it be easier for your component to get the `User` object as a prop? Both `withMappedNavigationAndConfigProps` and `withMappedNavigationProps` accept an optional second parameter, a react component that gets all the props and the wrapped component as a prop. Then you can pass all the props to the wrapped component and return it from render(). -->