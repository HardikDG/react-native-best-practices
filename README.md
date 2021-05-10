
#### Divide The ComponentsDivide The Components

Divide your React components into two directories - **components and containers.** You can name them differently as per your choice.

**containers** are directories which follow these rules:

- Do not import anything from react-native (View, Text, etc.) that build your JSX components.
- The imports to your Higher-Order Components connecting to the redux store (if your project uses redux which we recommend) and their respective connections. This means the redux hooks (useDispatch, useSelector, etc.), redux actions, redux selectors (extract useful data for the container from App state) must live here.
- Other possible imports could be react-navigation related integrations and possibly a unique library you use for your project which could be put here.
```
    // containers
    import React from 'react'
    import type { Element } from 'react'
    import { useDispatch, useSelector } from 'react-redux'
    import { NavigationScreenProp } from 'react-navigation'
    import I18n from 'react-native-i18n'
    import { Formik } from 'formik'
    import { registerProcess } from 'actions'
    import { authenticationSelector, ordersLoading } from 'selectors'
```

**components**  are directories which follow these rules:

-   components are children of a container and they would receive the App state data as props which would be used to construct the UI. You must place all the JSX code of your React components along with their respective react-native imports here.
-   You would also utilise the react hooks (useEffect, useRef, useState) at the component level which should mostly be avoided in a container.
-   Other possible imports would be types, styles, other components for code re-use, and in general anything else which a container does not do.

```
// components
import React, { useRef, useState } from 'react'
import {
  Animated,
  View,
  TouchableOpacity,
  Text,
} from 'react-native'
import { ArrowIcon } from 'components/icons'
import NutritionDetails from './NutritionDetails'
import type { NutritionalInfoType } from 'types/product'
import { nutritionalInfoStyles as styles } from './styles'
```

<br>
#### Create Aliases

Create aliases using babel-plugin-module-resolver to avoid nested imports such as import Product from `'../../../Components/Product'`. Aliases created using babel-plugin-module-resolver look something like this.

```
alias: {
          actions: './app/actions',
          api: './app/api',
          assets: './app/assets',
          components: './app/components',
          containers: './app/containers',
          constants: './app/constants',
          sagas: './app/sagas',
          selectors: './app/selectors',
          types: './app/types',
          utils: './app/utils',
}
```

You can use imports like import Product from `'components/Product'` after setting it up.

<br>
#### Sort the imports

Divide your imports and sort them logically unless it is not possible due to the use of aliases. There is no hard rule with the order in which you sort them, but at least categorizing them into two categories such as library imports and all other imports would be easier for someone reviewing your code.

```
import React, { useState } from 'react'
import type { Element } from 'react'
import { View, ScrollView } from 'react-native'
import { NavigationScreenProp } from 'react-navigation'
import { useSelector } from 'react-redux'
import type { OrderType, LineItemWithProdType } from 'types'
import { ordersByIdSelector, productsByIdSelector } from 'selectors'
import { OrderTicket, OrderDetails } from 'components/Order'
import { DefaultStatusBar, RedStatusBar } from 'components/StatusBar'
import { orderStyles as styles } from './styles'
```

<br>
#### Declare the types

Be it TypeScript or Flow, it is essential to declare the types of every object in the code. This includes declaring the return type and argument type.

```
const payload: LoginUserType = {
  email: 'name@example.com',
  password: 'password',
}
const roundDistance = (distance: number): string => (distance / 1000).
toFixed(1)
```

Sometimes it might be tempting to declare types (objects, props, state) inside each component (file). This invites trouble in the future if left unorganized into a separate types directory in a logical order based on the requirements of your project. Ensure the types created are reused across the code following the DRY principles to get the full benefits of type checking.

Also remember to use the | symbol to create Exact object types for even stronger type safety. This ensures new keys cannot be added to an object.

```
type LoginUserType = {|
  email: string,
  password: string,
|}
```
<br>
#### Separate The Styles
Among the various ways to deal with style, the React Native styling best practice is to separate the styles from the components.

Normally, to use styles better developers import the stylesheet from the React native library to create a separate style object.

There are various other ways to separate styles and components, but the basic benefit of doing so is that the codebase looks neat and easy on the eye of the reviewer for further analysis.

```
import { productAmountstyles as styles } from './styles'
...
<View style={styles.container}>
  <Text style={styles.amountText}>{I18n.t('itemScreen.amount')}</Text>
  <View style={styles.quantityContainer}>
    <CircularButton disabled={quantity === 1} onPress={onReduce} />
    <View style={styles.quantityTextContainer}>
      <Text style={styles.quantityText}>{quantity}</Text>
    </View>
    <CircularButton disabled={false} add onPress={onAdd} />
  </View>
</View>
```

<br>
#### Use Hooks


 you are writing a function component, and you realize that there is a need to add a state, in such cases hooks come off as a great helping tool. Nowdays you can write most of the code with the help of hook which can reduce the code and improve the readablity

```
 const [showModal, setShowModal] = useState(true)
...
useEffect(() => {
  dispatch(doFetchOrders())
  dispatch(doFetchProducts())
}, [dispatch])
...
const authenticationData: AuthenticationStateType = useSelector
(authenticationSelector)
...
const mapViewRef: { current: MapView } = useRef(null)
```

<br>
#### Manage with Redux
Redux is still useful and has a predictable way to handle the App state in our projects. Use it with [React Native debugger](https://github.com/jhen0409/react-native-debugger "React Native debugger"), which is packed with a lot of useful features such as viewing the entire App state tree (giant JavaScript object), firing redux actions on the fly to see it reflect on the App UI and so on.

<br>
#### Aggregate The Selectors

 Selector is a function that accepts the Redux state in the form of an argument and routes the data back which is extracted from the state.

To put it plainly, selectors are functional elements that are added to tracking and retracting snippets of data from the internalized components of a Redux store.

Creating an aggregated selectors directory helps in reusing the functions across several components.

We can use the reselect library which comes with innate benefits of memorization and cache.

```
export const productsSelector = state => state.menuItems.products.map(({
  product }) => product)

export const productsByIdSelector = createSelector(
  productsSelector,
  (products) => products.reduce((prodObj, product) => (
    prodObj[product.id] = product), {}),
)
...
const productsById = useSelector(productsByIdSelector)
```

<br>
#### Use Jest

TDD and writing clear tests are important, and lead to fewer future bugs. Different parts of React Native Apps can be tested in different ways, and We should implement at least the Snapshot tests and Redux tests

<br>
#### Classify The Components
We should have 2 type of components 
Presentation Component and Container component

**Presentational component**
- Primarily deals with how a component looks in the final UI framework
- Does not provide info about data loading and mutable option
- Uses props to callback and receive data

<br>
**Container component**
- It contains info about the component
- Arms the presentational component with stateful properties

<br>
#### Key of Each Element Should Be Unique
React is working on Shadow dom to update specific part of list. If list has same key it will have a direct impact on the performance of the app, as it can slow down the app’s functionality. Thus, it is advised to use a unique key for each element in the list. Also it will show the error also for the non-unique key

```
const todoItems = todos.map((todo) =>
      <li key={todo._id}>
        {todo.text}
      </li>
    );
```

The most efficient way to add a unique key to each element is to assign a particular id. An ‘id’ is assigned to each element making it simple to represent changes for a particular element.

<br>
#### Manage Static Images

As per the RN doc, if you want to add a static image, you must specify it using the ‘require’ tag in the source code tree.

Using the ‘require’ tag, you can easily import the image to the source attribute of the concerned image component.

The repression of the required tag is given below.

`<Image source={require(‘./my-icon.png’)} />`

There are several advantages of specifying the image using ‘require’. For instance, it fetches the right images adhering to dimension without really going down the path of image collision due to the naming convention.

```
// BAD
  const icon = this.props.active ? 'icon-innofied-active' : 'icon-innofied-inactive';
  <Image source={require('./' + icon + '.png')} />;

 // GOOD
  const icon = this.props.active ? require('./icon-innofied-active.png')
   : require('./icon-innofied-inactive.png');
  <Image source={icon} />;
  ```

<br>
#### Using Platform Specific Code & Style

Using platform specific code is one of the effective React Native Best Practices.This allowed us to develop an application that fits both iOS and Android versions.

  ```
import { Platform, StyleSheet } from 'react-native';

const styles = StyleSheet.create({
 container: {
   flex: 1,
   ...Platform.select({
     ios: {
       fontFamily: 'Arial',
     },
     android: {
       fontFamily: 'Roboto',
     },
   }),
 },
});
  ```

There are two specific APIs: Platform.OS and Platform.select. These APIs help to gauge the native platform and apply appropriate styles.

<br>
#### Locking Dependencies

Dependencies make the task of developing an app more holistic and easier, but at the same time, you must employ dependencies that are of utmost necessity.

Now, when adding dependencies based on regular needs, managing them becomes an uphill task. Thus, if you add a dependency, you must lock the range or the versions.

It saves a lot of time and effort. Staying attuned to changes is quite tough, especially with Javascript as things (changes) move fast in Js library.

  ```
"react-native-img-cache": "^1.5.3",
"react-native-fetch-blob": "^0.10.7",
"react-native-linear-gradient": "^2.3.0"
  ```

Instead of this:
  ```
"react-native-img-cache": "1.5.3",
"react-native-fetch-blob": "0.10.7",
"react-native-linear-gradient": "2.3.0"
  ```

Take Help of UI Frameworks
The basic idea of React was to develop a UI framework that delivers the best user experience. It was built to augment the process of creating UI templates using JS thread.

The most commonly used UI framework is [Galio.io](https://galio.io/ "Galio.io").[NativeBase](https://nativebase.io/ "NativeBase")  Such a platform not only augments the rate of build, but it also provides for selecting specific components.

<br>
#### Do not Put Logs In Release Build

Putting logs in release builds is a no-no, as it slows down your app. This true for most of the logger types present out there, especially if you work in a redux logger.

Excess use of the console.log statement which initiates the thread of log for keeping track of events and warnings is the cause for slowing down the application.

<br>
#### Use Safe Area View
In the newer versions of the iPhone, the sensor and the home button overlap with the nav and tab bar components. It is one of the effective React Native Security Best Practices.

This creates a bad user experience and also the interface looks murky. To solve this issue React Native introduced something called safe area view.

In the landscape mode, the content seems to overlap with the sensor cluster, so in the view tag for content, you need to import the safe area view.

<br>
#### Do not Use TouchWithoutFeedback

 RN includes touchable button components in various forms.

The list includes: TouchableHighlight, TouchableOpacity, Touchable Native Feedback, and finally touchable without feedback, this is where the developer needs to be extremely cautious.

The docs available on the RN official website states you should have a good reason for using TouchWithoutFeeedback, as each element must exude visual feedback when clicked.

You can use this touchable element when you do not want to trigger animated feedback when initiating action.

<br>
#### Do not Use Expo

Expo does bring in several advantages to the table and there is no denying this fact. For instance, with Expo, the developers can carve a fully functional cross-platform native without having to learn Xcode or Java (Android studio).

But by using Expo developers ignores flow coming in the Native applications which can come at the time of Native building and testing so it is better to start without Expo 
