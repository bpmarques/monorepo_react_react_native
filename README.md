# React/React Native monorepo!

A monorepo using yarn workspaces to build a project in React, a project in React Native and a common library to share code between them.

## Folders Structure
```
Project Root
+-- package.json
+-- node_modules
+-- packages
|   +-- react app
|   |   +-- node_modules
|   |   +-- package.json
|   |   +-- src
|   +-- react native app
|   |   +-- node_modules
|   |   +-- package.json
|   |   +-- src
|   +-- common
|   |   +-- node_modules
|   |   +-- package.json
|   |   +-- src
```
## How to create the monorepo

 - ```mkdir <folder_name>```
 - ```cd <folder_name> && yarn init```
 - Add the following snippet in the root package.json to enable yarn workspaces
 ```json
"workspaces": {  
  "packages": [  
    "packages/*"  
  ],  
  "nohoist": ["**/react-native", "**/react-native/**"]  
},
```
 - ```mkdir packages  && cd packages```
 - ```react-native init mobile```
 - ```cd mobile && yarn run android```
 - ```cd .. && npx create-react-app web```
 - ```cd web && yarn start```
 - ```cd .. && mkdir common && cd common```
 - ```yarn init```
 - ```mkdir src```
 - Create the file main.js with the following content
```javascript
//packages/common/src/main.js
export const getWelcomeString = (name) =>{  
   return `Welcome ${name} to our awesome monorepo!`  
}
```
 - Adding the common dependcy to the mobile project
```json
//dependency section of packages/mobile/package.json
"dependencies": {  
  "react": "16.9.0",  
  "react-native": "0.61.5",  
  "common": "1.0.0"
},
```
 - In order to the metro packager search the common dependency in the right folder, the following change needs to be done.
```javascript
//packages/mobile/metro.config.js  
/**
* Metro configuration for React Native
* https://github.com/facebook/react-native
*
* @format
*/

const path = require('path');

module.exports = {  
  transformer: {  
    getTransformOptions: async () => ({  
      transform: {  
        experimentalImportSupport: false,  
        inlineRequires: false,  
      },  
    }),  
  },  
  resolver: {  
    extraNodeModules: new Proxy(  
      {},  
      {  
        get: (target, name) => {  
          return path.join(__dirname, `node_modules/${name}`);  
        },  
      },  
    ),  
  },  
  watchFolders: [path.resolve(__dirname, '../')],
};
```
 - Update the App.js to use the common function
```javascript
/**
* Sample React Native App
* https://github.com/facebook/react-native
*
* @format
* @flow
*/

import  React  from  'react';

import {
  SafeAreaView,
  StyleSheet,
  ScrollView,
  View,
  Text,
  StatusBar,
} from  'react-native';

import {
  Header,
  LearnMoreLinks,
  Colors,
  DebugInstructions,
  ReloadInstructions,
} from  'react-native/Libraries/NewAppScreen';

import { getWelcomeString } from  'common/src/main';

const  App: () =>  React$Node  = () => {
  return (
    <>
      <StatusBar  barStyle="dark-content"  />
      <SafeAreaView>
        <ScrollView
          contentInsetAdjustmentBehavior="automatic"
          style={styles.scrollView}>
          <Header  />
          {global.HermesInternal == null ? null : (
          	<View  style={styles.engine}>
          	  <Text  style={styles.footer}>Engine: Hermes</Text>
          	</View>
          )}
          <View  style={styles.body}>
            <View  style={styles.sectionContainer}>
              <Text  style={styles.sectionDescription}>{getWelcomeString("Mobile Application")}</Text>
              <Text  style={styles.sectionTitle}>Step One</Text>
              <Text  style={styles.sectionDescription}>
                Edit <Text  style={styles.highlight}>App.js</Text> to change this
                screen and then come back to see your edits.
              </Text>
            </View>
            <View  style={styles.sectionContainer}>
              <Text  style={styles.sectionTitle}>See Your Changes</Text>
              <Text  style={styles.sectionDescription}>
                <ReloadInstructions  />
              </Text>
            </View>
            <View  style={styles.sectionContainer}>
              <Text  style={styles.sectionTitle}>Debug</Text>
              <Text  style={styles.sectionDescription}>
                <DebugInstructions  />
              </Text>
            </View>
            <View  style={styles.sectionContainer}>
              <Text  style={styles.sectionTitle}>Learn More</Text>
              <Text  style={styles.sectionDescription}>
                Read the docs to discover what to do next:
              </Text>
            </View>
            <LearnMoreLinks  />
          </View>
        </ScrollView>
      </SafeAreaView>
    </>
  );
};

const  styles  =  StyleSheet.create({
  scrollView: {
    backgroundColor:  Colors.lighter,
  },
  engine: {
    position:  'absolute',
    right:  0,
  },
  body: {
    backgroundColor:  Colors.white,
  },
  sectionContainer: {
    marginTop:  32,
    paddingHorizontal:  24,
  },
  sectionTitle: {
    fontSize:  24,
    fontWeight:  '600',
    color:  Colors.black,
  },
  sectionDescription: {
    marginTop:  8,
    fontSize:  18,
    fontWeight:  '400',
    color:  Colors.dark,
  },
  highlight: {
    fontWeight:  '700',
  },
  footer: {
    color:  Colors.dark,
    fontSize:  12,
    fontWeight:  '600',
    padding:  4,
    paddingRight:  12,
    textAlign:  'right',
  },
});

export  default  App;
```
 - Now, add the common dependecy to the web project
```json
"dependencies": {
  "@testing-library/jest-dom": "^4.2.4",
  "@testing-library/react": "^9.3.2",
  "@testing-library/user-event": "^7.1.2",
  "jest": "24.9.0",
  "react": "^16.12.0",
  "react-dom": "^16.12.0",
  "react-scripts": "3.3.1",
  "common": "1.0.0"
},
```
 - Update the App.js to use the common function
```javascript
import  React  from  'react';
import  logo  from  './logo.svg';
import  './App.css';
import { getWelcomeString } from  'common/src/main';

function  App() {
  return (
    <div  className="App">
      <header  className="App-header">
        <img  src={logo}  className="App-logo"  alt="logo"  />
        <p>
          Edit <code>src/App.js</code> and save to reload.
        </p>
        <p>{  getWelcomeString("Web Application") }</p>
        <a className="App-link"
          href="https://reactjs.org"
          target="_blank"
          rel="noopener noreferrer">
          Learn React
        </a>
      </header>
    </div>
  );
}

export  default  App;
```