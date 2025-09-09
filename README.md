# React Native Cheat Sheet

## Quick Reference

React Native lets you build mobile apps using JavaScript and React. It creates truly native mobile applications.

### Setup and Installation

```bash
# Install React Native CLI
npm install -g @react-native-community/cli

# Create new project
npx react-native init MyApp

# For Expo managed workflow
npm install -g expo-cli
expo init MyProject

# Install dependencies
cd MyApp && npm install

# Run on iOS
npx react-native run-ios

# Run on Android
npx react-native run-android

# Start Metro bundler
npx react-native start
```

## Core Concepts and Architecture

### Components

```javascript
// Functional Component
import React from 'react';
import { View, Text, StyleSheet } from 'react-native';

const MyComponent = ({ title, children }) => {
  return (
    <View style={styles.container}>
      <Text style={styles.title}>{title}</Text>
      {children}
    </View>
  );
};

// Class Component
class MyClassComponent extends React.Component {
  render() {
    return (
      <View>
        <Text>{this.props.title}</Text>
      </View>
    );
  }
}
```

### Essential Components

```javascript
// Basic UI Components
import {
  View,
  Text,
  ScrollView,
  FlatList,
  SectionList,
  Image,
  TextInput,
  TouchableOpacity,
  TouchableHighlight,
  Pressable,
  Button,
  Alert,
  ActivityIndicator,
  Modal,
  Switch,
  Slider
} from 'react-native';

// View (like div in web)
<View style={styles.container}>
  <Text>Hello World</Text>
</View>

// Text
<Text style={styles.text} numberOfLines={2}>
  Long text content...
</Text>

// TextInput
<TextInput
  style={styles.input}
  placeholder="Enter text"
  value={text}
  onChangeText={setText}
  keyboardType="email-address"
  secureTextEntry={true}
/>

// TouchableOpacity
<TouchableOpacity
  style={styles.button}
  onPress={() => console.log('Pressed')}
  activeOpacity={0.7}
>
  <Text>Press me</Text>
</TouchableOpacity>

// Image
<Image
  source={{ uri: 'https://example.com/image.jpg' }}
  source={require('./assets/logo.png')}
  style={styles.image}
  resizeMode="cover"
/>

// FlatList
<FlatList
  data={data}
  renderItem={({ item }) => (
    <Text>{item.name}</Text>
  )}
  keyExtractor={item => item.id}
  refreshing={refreshing}
  onRefresh={onRefresh}
  onEndReached={loadMore}
  onEndReachedThreshold={0.1}
/>
```

## State Management

### useState Hook

```javascript
import React, { useState, useEffect } from 'react';

const MyComponent = () => {
  const [count, setCount] = useState(0);
  const [user, setUser] = useState(null);
  const [loading, setLoading] = useState(true);

  useEffect(() => {
    // Side effects
    fetchUser();
    return () => {
      // Cleanup
    };
  }, []);

  const fetchUser = async () => {
    try {
      const response = await fetch('/api/user');
      const userData = await response.json();
      setUser(userData);
    } catch (error) {
      console.error(error);
    } finally {
      setLoading(false);
    }
  };

  return (
    <View>
      <Text>Count: {count}</Text>
      <Button 
        title="Increment" 
        onPress={() => setCount(count + 1)} 
      />
    </View>
  );
};
```

### Context API

```javascript
// UserContext.js
import React, { createContext, useContext, useState } from 'react';

const UserContext = createContext();

export const UserProvider = ({ children }) => {
  const [user, setUser] = useState(null);

  const login = async (credentials) => {
    // Login logic
    setUser(userData);
  };

  const logout = () => {
    setUser(null);
  };

  return (
    <UserContext.Provider value={{ user, login, logout }}>
      {children}
    </UserContext.Provider>
  );
};

export const useUser = () => {
  const context = useContext(UserContext);
  if (!context) {
    throw new Error('useUser must be used within UserProvider');
  }
  return context;
};
```

### Redux Toolkit

```javascript
// store.js
import { configureStore } from '@reduxjs/toolkit';
import counterSlice from './counterSlice';

export const store = configureStore({
  reducer: {
    counter: counterSlice,
  },
});

// counterSlice.js
import { createSlice } from '@reduxjs/toolkit';

const counterSlice = createSlice({
  name: 'counter',
  initialState: {
    value: 0,
  },
  reducers: {
    increment: (state) => {
      state.value += 1;
    },
    decrement: (state) => {
      state.value -= 1;
    },
    incrementByAmount: (state, action) => {
      state.value += action.payload;
    },
  },
});

export const { increment, decrement, incrementByAmount } = counterSlice.actions;
export default counterSlice.reducer;

// Component usage
import { useSelector, useDispatch } from 'react-redux';
import { increment, decrement } from './counterSlice';

const Counter = () => {
  const count = useSelector(state => state.counter.value);
  const dispatch = useDispatch();

  return (
    <View>
      <Text>{count}</Text>
      <Button title="+" onPress={() => dispatch(increment())} />
      <Button title="-" onPress={() => dispatch(decrement())} />
    </View>
  );
};
```

## Navigation

### React Navigation v6

```bash
# Install dependencies
npm install @react-navigation/native
npm install react-native-screens react-native-safe-area-context

# Stack navigation
npm install @react-navigation/native-stack

# Tab navigation
npm install @react-navigation/bottom-tabs

# Drawer navigation
npm install @react-navigation/drawer
```

```javascript
// App.js
import { NavigationContainer } from '@react-navigation/native';
import { createNativeStackNavigator } from '@react-navigation/native-stack';
import { createBottomTabNavigator } from '@react-navigation/bottom-tabs';

const Stack = createNativeStackNavigator();
const Tab = createBottomTabNavigator();

// Stack Navigator
const StackNavigator = () => (
  <Stack.Navigator
    screenOptions={{
      headerStyle: { backgroundColor: '#6200ea' },
      headerTintColor: '#fff',
    }}
  >
    <Stack.Screen 
      name="Home" 
      component={HomeScreen} 
      options={{ title: 'My Home' }}
    />
    <Stack.Screen 
      name="Details" 
      component={DetailsScreen} 
      options={({ route }) => ({ title: route.params.name })}
    />
  </Stack.Navigator>
);

// Tab Navigator
const TabNavigator = () => (
  <Tab.Navigator
    screenOptions={({ route }) => ({
      tabBarIcon: ({ focused, color, size }) => {
        // Icon logic
      },
      tabBarActiveTintColor: 'tomato',
      tabBarInactiveTintColor: 'gray',
    })}
  >
    <Tab.Screen name="Home" component={HomeScreen} />
    <Tab.Screen name="Settings" component={SettingsScreen} />
  </Tab.Navigator>
);

// Main App
const App = () => (
  <NavigationContainer>
    <StackNavigator />
  </NavigationContainer>
);

// Navigation in components
const HomeScreen = ({ navigation }) => (
  <View>
    <Button
      title="Go to Details"
      onPress={() => 
        navigation.navigate('Details', { 
          itemId: 86, 
          name: 'Item Name' 
        })
      }
    />
  </View>
);
```

## Styling

### StyleSheet

```javascript
import { StyleSheet, Dimensions } from 'react-native';

const { width, height } = Dimensions.get('window');

const styles = StyleSheet.create({
  container: {
    flex: 1,
    backgroundColor: '#fff',
    alignItems: 'center',
    justifyContent: 'center',
  },
  text: {
    fontSize: 16,
    fontWeight: 'bold',
    color: '#333',
    marginBottom: 10,
  },
  button: {
    backgroundColor: '#6200ea',
    padding: 15,
    borderRadius: 8,
    alignItems: 'center',
    marginTop: 10,
    width: width * 0.8, // Responsive width
  },
  buttonText: {
    color: 'white',
    fontSize: 16,
    fontWeight: 'bold',
  },
});
```

### Flexbox Layout

```javascript
const styles = StyleSheet.create({
  // Flex container
  container: {
    flex: 1, // Take all available space
    flexDirection: 'row', // 'row' | 'column' (default)
    justifyContent: 'center', // Main axis alignment
    alignItems: 'center', // Cross axis alignment
    flexWrap: 'wrap', // Allow wrapping
  },
  
  // Flex items
  item: {
    flex: 1, // Equal space
    flexGrow: 1, // Grow factor
    flexShrink: 1, // Shrink factor
    flexBasis: 'auto', // Initial size
    alignSelf: 'stretch', // Override container's alignItems
  },
});
```

## Native Modules and Platform APIs

### Platform-specific code

```javascript
import { Platform } from 'react-native';

// Platform check
if (Platform.OS === 'ios') {
  // iOS specific code
} else if (Platform.OS === 'android') {
  // Android specific code
}

// Platform select
const styles = StyleSheet.create({
  container: {
    ...Platform.select({
      ios: {
        shadowColor: '#000',
        shadowOffset: { width: 0, height: 2 },
        shadowOpacity: 0.25,
        shadowRadius: 3.84,
      },
      android: {
        elevation: 5,
      },
    }),
  },
});

// Platform-specific files
// MyComponent.ios.js
// MyComponent.android.js
```

### Permissions

```javascript
import { PermissionsAndroid, Platform } from 'react-native';

const requestCameraPermission = async () => {
  if (Platform.OS === 'android') {
    try {
      const granted = await PermissionsAndroid.request(
        PermissionsAndroid.PERMISSIONS.CAMERA,
        {
          title: 'Camera Permission',
          message: 'App needs camera permission',
          buttonNeutral: 'Ask Me Later',
          buttonNegative: 'Cancel',
          buttonPositive: 'OK',
        },
      );
      return granted === PermissionsAndroid.RESULTS.GRANTED;
    } catch (err) {
      console.warn(err);
      return false;
    }
  }
  return true; // iOS handles permissions differently
};
```

### Async Storage

```javascript
import AsyncStorage from '@react-native-async-storage/async-storage';

// Store data
const storeData = async (key, value) => {
  try {
    await AsyncStorage.setItem(key, JSON.stringify(value));
  } catch (error) {
    console.error('Error storing data:', error);
  }
};

// Retrieve data
const getData = async (key) => {
  try {
    const value = await AsyncStorage.getItem(key);
    return value != null ? JSON.parse(value) : null;
  } catch (error) {
    console.error('Error retrieving data:', error);
    return null;
  }
};

// Remove data
const removeData = async (key) => {
  try {
    await AsyncStorage.removeItem(key);
  } catch (error) {
    console.error('Error removing data:', error);
  }
};
```

## Common Patterns and Best Practices

### Custom Hooks

```javascript
// useApi hook
import { useState, useEffect } from 'react';

const useApi = (url) => {
  const [data, setData] = useState(null);
  const [loading, setLoading] = useState(true);
  const [error, setError] = useState(null);

  useEffect(() => {
    const fetchData = async () => {
      try {
        setLoading(true);
        const response = await fetch(url);
        const result = await response.json();
        setData(result);
      } catch (err) {
        setError(err);
      } finally {
        setLoading(false);
      }
    };

    fetchData();
  }, [url]);

  return { data, loading, error };
};

// Usage
const MyComponent = () => {
  const { data, loading, error } = useApi('/api/users');

  if (loading) return <ActivityIndicator />;
  if (error) return <Text>Error: {error.message}</Text>;
  
  return (
    <FlatList
      data={data}
      renderItem={({ item }) => <Text>{item.name}</Text>}
    />
  );
};
```

### Error Boundaries

```javascript
import React from 'react';
import { Text, View } from 'react-native';

class ErrorBoundary extends React.Component {
  constructor(props) {
    super(props);
    this.state = { hasError: false };
  }

  static getDerivedStateFromError(error) {
    return { hasError: true };
  }

  componentDidCatch(error, errorInfo) {
    console.log('Error caught by boundary:', error, errorInfo);
    // Log to crash reporting service
  }

  render() {
    if (this.state.hasError) {
      return (
        <View style={{ flex: 1, justifyContent: 'center', alignItems: 'center' }}>
          <Text>Something went wrong.</Text>
        </View>
      );
    }

    return this.props.children;
  }
}

// Usage
<ErrorBoundary>
  <MyApp />
</ErrorBoundary>
```

## Testing and Debugging

### Jest Testing

```javascript
// __tests__/MyComponent.test.js
import React from 'react';
import { render, fireEvent } from '@testing-library/react-native';
import MyComponent from '../MyComponent';

describe('MyComponent', () => {
  it('renders correctly', () => {
    const { getByText } = render(<MyComponent title="Test" />);
    expect(getByText('Test')).toBeTruthy();
  });

  it('handles button press', () => {
    const mockOnPress = jest.fn();
    const { getByText } = render(
      <MyComponent title="Test" onPress={mockOnPress} />
    );
    
    fireEvent.press(getByText('Press me'));
    expect(mockOnPress).toHaveBeenCalled();
  });
});

// Testing async operations
it('fetches user data', async () => {
  const mockFetch = jest.spyOn(global, 'fetch')
    .mockResolvedValueOnce({
      json: jest.fn().mockResolvedValueOnce({ name: 'John' }),
    });

  const { findByText } = render(<UserProfile userId="1" />);
  
  await findByText('John');
  expect(mockFetch).toHaveBeenCalledWith('/api/users/1');
});
```

### Debugging

```javascript
// Console logging
console.log('Debug info:', data);
console.warn('Warning message');
console.error('Error occurred');

// React Native Debugger
// Install: https://github.com/jhen0409/react-native-debugger

// Flipper (Facebook's debugging platform)
// Built-in with React Native 0.62+

// Debug with VS Code
// Install React Native Tools extension
```

## Performance Optimization

### Optimization Techniques

```javascript
// Use React.memo for functional components
const MyComponent = React.memo(({ data }) => {
  return <Text>{data.name}</Text>;
});

// Use useMemo for expensive calculations
const ExpensiveComponent = ({ items }) => {
  const expensiveValue = useMemo(() => {
    return items.reduce((acc, item) => acc + item.value, 0);
  }, [items]);

  return <Text>{expensiveValue}</Text>;
};

// Use useCallback for event handlers
const ParentComponent = () => {
  const [count, setCount] = useState(0);

  const handlePress = useCallback(() => {
    setCount(prev => prev + 1);
  }, []);

  return <ChildComponent onPress={handlePress} />;
};

// Optimize FlatList
<FlatList
  data={data}
  renderItem={({ item }) => <Item data={item} />}
  keyExtractor={item => item.id}
  getItemLayout={(data, index) => ({
    length: ITEM_HEIGHT,
    offset: ITEM_HEIGHT * index,
    index,
  })}
  removeClippedSubviews={true}
  maxToRenderPerBatch={10}
  updateCellsBatchingPeriod={50}
  initialNumToRender={10}
  windowSize={10}
/>

// Image optimization
<Image
  source={{ uri: imageUrl }}
  style={styles.image}
  resizeMode="cover"
  loadingIndicatorSource={require('./loading.gif')}
  defaultSource={require('./placeholder.png')}
/>
```

### Memory Management

```javascript
// Clean up subscriptions in useEffect
useEffect(() => {
  const subscription = SomeAPI.subscribe(callback);
  
  return () => {
    subscription.unsubscribe();
  };
}, []);

// Remove event listeners
useEffect(() => {
  const listener = DeviceEventEmitter.addListener('event', handler);
  
  return () => {
    listener.remove();
  };
}, []);
```

## Deployment and Distribution

### Android Build

```bash
# Generate signed APK
cd android
./gradlew assembleRelease

# Generate AAB (recommended for Play Store)
./gradlew bundleRelease

# Install release build
npx react-native run-android --variant=release
```

### iOS Build

```bash
# Build for device
npx react-native run-ios --device --configuration Release

# Archive in Xcode
# Product -> Archive -> Distribute App
```

### App Signing

```javascript
// android/app/build.gradle
android {
  signingConfigs {
    release {
      storeFile file(MYAPP_RELEASE_STORE_FILE)
      storePassword MYAPP_RELEASE_STORE_PASSWORD
      keyAlias MYAPP_RELEASE_KEY_ALIAS
      keyPassword MYAPP_RELEASE_KEY_PASSWORD
    }
  }
  buildTypes {
    release {
      signingConfig signingConfigs.release
      minifyEnabled enableProguardInReleaseBuilds
      proguardFiles getDefaultProguardFile("proguard-android.txt"), "proguard-rules.pro"
    }
  }
}
```

## Popular Libraries

### UI Components

```bash
# React Native Elements
npm install react-native-elements react-native-vector-icons

# Native Base
npm install native-base react-native-svg

# React Native Paper
npm install react-native-paper

# React Native UI Lib
npm install react-native-ui-lib
```

### Utilities

```bash
# Navigation
npm install @react-navigation/native

# State Management
npm install @reduxjs/toolkit react-redux
npm install zustand

# Form Handling
npm install react-hook-form

# HTTP Client
npm install axios

# Date/Time
npm install date-fns moment

# Image Picker
npm install react-native-image-picker

# Camera
npm install react-native-camera

# Maps
npm install react-native-maps

# Push Notifications
npm install @react-native-firebase/messaging

# Biometric Authentication
npm install react-native-biometrics
```

## Troubleshooting

### Common Issues

```bash
# Metro bundler issues
npx react-native start --reset-cache

# Clean build
# Android
cd android && ./gradlew clean && cd ..
npx react-native run-android

# iOS
cd ios && xcodebuild clean && cd ..
npx react-native run-ios

# Node modules issues
rm -rf node_modules
npm install

# Watchman issues (macOS)
brew install watchman
watchman watch-del-all
```

### Android Specific

```bash
# Enable USB debugging
# Settings -> Developer Options -> USB Debugging

# Check connected devices
adb devices

# Reverse port for development
adb reverse tcp:8081 tcp:8081

# Clear app data
adb shell pm clear com.myapp
```

### iOS Specific

```bash
# Clean derived data
rm -rf ~/Library/Developer/Xcode/DerivedData

# Reset iOS simulator
xcrun simctl erase all

# List simulators
xcrun simctl list devices
```

## Official Resources

- [React Native Documentation](https://reactnative.dev/docs/getting-started)
- [React Navigation](https://reactnavigation.org/)
- [React Native Directory](https://reactnative.directory/)
- [React Native Blog](https://reactnative.dev/blog/)
- [Metro Bundler](https://facebook.github.io/metro/)
- [Flipper Debugger](https://fbflipper.com/)
- [React Native CLI](https://github.com/react-native-community/cli)

## Community and Tools

- [Awesome React Native](https://github.com/jondot/awesome-react-native)
- [React Native Community](https://github.com/react-native-community)
- [Expo](https://expo.dev/)
- [Ignite CLI](https://github.com/infinitered/ignite)
- [Reactotron](https://github.com/infinitered/reactotron)