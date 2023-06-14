
# Async Storage
### Add 
```
npm i @react-native-async-storage/async-storage
```
### Create a basic structure (adding two buttons)
```
export default function App() {
  return (
    <View style={styles.container}>
      <View style={{ padding: 20 , width:'100%'}}>
        <Button title='Set Data' onPress={() => console.log('Set Data')} ></Button>
      </View>

      <View style={{ padding: 20 , width:'100%'}}>
        <Button title='Show Data' onPress={() => console.log('Show Data')} ></Button>
      </View>

    </View>
  );
}
```
### Import 
```
import AsyncStorage from '@react-native-async-storage/async-storage';
```

### Create function to set Data in storage
`setItem()` is used both to add new data item (when no data for given key exists), and to modify existing item (when previous data for given key exists).
```
  const setData =()=>{
    // setItem(key, value)
    AsyncStorage.setItem("name", "Muhammad Naqeeb");
  }
```
### Create function to showData 
`getItem()` returns a promise that either resolves to stored value when data is found for given key, or returns null otherwise.
getItem return a promise so we need to await on it
```
  const showData = async ()=>{
    const name = await AsyncStorage.getItem("name");
    console.log(name);
  }
```
🔑 Note: 
Every thing store in AsyncStorage should be a string
```
AsyncStorage.setItem("age",24); // 
```
`give error`

Error: [AsyncStorage] The value for key "age" is not a string.

### Storing object
As it setItem only stores sting so we need to convert object into string by `JSON.stringify()` function.
```
const setData =()=>{  
    // AsyncStorage.setItem("name", "Muhammad Naqeeb");
    const user_details = {
      name: 'Muhammad Naqeeb',
      age:23
    }
    AsyncStorage.setItem("user_details", JSON.stringify(user_details));

  }
```
```
const showData = async ()=>{
    const details = await AsyncStorage.getItem("user_details");
    console.log(details);
  }
```
![](001)
But this data is string so, you cannot do details[‘name’], so you have to convert this string back to object by `JSON.parse` method.
```
  const showData = async ()=>{
    const details = await AsyncStorage.getItem("user_details");
    user_detail = JSON.parse(details);
    console.log(user_detail['age']);
  }
```

### Showing data on screen
```
const [name, setName]= useState(null);
const [age, setAge]= useState(null);
```
In App function return 
```
{
  name?(<Text>Name: {name}</Text>): null
}
{
  age?(<Text>Age: {age}</Text>): null
}
```
In  show data function
```
  const showData = async ()=>{
    const details = await AsyncStorage.getItem("user_details");
    user_detail = JSON.parse(details);
    setName(user_detail['name']); // <-- These here
    setAge(user_detail['age']); // <-- These here
  }
```

### Clear Data
This will remove all data from Async Storage
```
const clearData = ()=>{
    AsyncStorage.clear();
}
```
### removeItem()
Removes an item for a key and invokes a callback upon completion. Returns a Promise object.
```
const removeWithKey=async ()=>{
    // you have to provide key
    await AsyncStorage.removeItem("dataKey");
}
```

### getAllKeys
```
const getAllKeys = async () => {
    let keys = []
    keys = await AsyncStorage.getAllKeys()
    console.log(keys)
}
```
### multiGet
Fetches multiple key-value pairs for given array of keys in a batch.
```
getMultiple = async () => {

  let values
  try {
    values = await AsyncStorage.multiGet(['@MyApp_user', '@MyApp_key'])
  } catch(e) {
    // read error
  }
  console.log(values)

  // example console.log output:
  // [ ['@MyApp_user', 'myUserValue'], ['@MyApp_key', 'myKeyValue'] ]
}
```
### multiSet
Stores multiple key-value pairs in a batch. 
```
multiSet = async () => {
  const firstPair = ["@MyApp_user", "value_1"]
  const secondPair = ["@MyApp_key", "value_2"]
  try {
    await AsyncStorage.multiSet([firstPair, secondPair])
  } catch(e) {
    //save error
  }

  console.log("Done.")
}
```

### multiRemove
Clears multiple key-value entries for given array of keys in a batch. 
```
removeFew = async () => {
  const keys = ['@MyApp_USER_1', '@MyApp_USER_2']
  try {
    await AsyncStorage.multiRemove(keys)
  } catch(e) {
    // remove error
  }

  console.log('Done')
}
```
### mergeItem
Merges an existing value stored under key, with new value, assuming both values are stringified JSON.
```
 // merge
 const USER_1 = {
  name: 'Ali',
  age: 20,
  traits: {
    hair: 'black',
    eyes: 'blue'
  }
}

const USER_2 = {
  name: 'Bilal',
  age: 21,
  hobby: 'cars',
  traits: {
    eyes: 'green',
  }
}

mergeUsers = async () => {
  try {
    //save first user
    await AsyncStorage.setItem('@MyApp_user', JSON.stringify(USER_1))

    // merge USER_2 into saved USER_1
    await AsyncStorage.mergeItem('@MyApp_user', JSON.stringify(USER_2))

    // read merged item
    const currentUser = await AsyncStorage.getItem('@MyApp_user')

    console.log(currentUser)

    // console.log result:
    // {
    //   name: 'Bilal',
    //   age: 21,
    //   hobby: 'cars',
    //   traits: {
    //     eyes: 'green',
    //     hair: 'black'
    //   }
    // }
  }catch(e){

  }
}
```

### Complete code for Async Storage
```
import AsyncStorage from '@react-native-async-storage/async-storage';
import { useState } from 'react';
import { Button, StyleSheet, Text, View } from 'react-native';

export default function App() {
  const [name, setName]= useState(null);
  const [age, setAge]= useState(null);

  const setData =()=>{
    // AsyncStorage.setItem("name", "Muhammad Naqeeb");
    const user_details = {
      name: 'Muhammad Naqeeb',
      age:23
    }
    AsyncStorage.setItem("user_details", JSON.stringify(user_details));
    AsyncStorage.setItem("dataKey", "Hi");
  }

  const showData = async ()=>{
    const details = await AsyncStorage.getItem("user_details");
    user_detail = JSON.parse(details);
    setName(user_detail['name']); // <-- These here
    setAge(user_detail['age']); // <-- These here
    
    const newData = await AsyncStorage.getItem("dataKey");
    console.log(newData);
  }
  const clearData = ()=>{
    AsyncStorage.clear();
  }
  const removeWithKey=async ()=>{
    // you have to provide key
    await AsyncStorage.removeItem("dataKey");
  }

  const getAllKeys = async () => {
    let keys = []
    keys = await AsyncStorage.getAllKeys()
    console.log(keys)
  }
 // merge
 const USER_1 = {
  name: 'Ali',
  age: 20,
  traits: {
    hair: 'black',
    eyes: 'blue'
  }
}

const USER_2 = {
  name: 'Bilal',
  age: 21,
  hobby: 'cars',
  traits: {
    eyes: 'green',
  }
}

mergeUsers = async () => {
  try {
    //save first user
    await AsyncStorage.setItem('@MyApp_user', JSON.stringify(USER_1))
    // merge USER_2 into saved USER_1
    await AsyncStorage.mergeItem('@MyApp_user', JSON.stringify(USER_2))
    // read merged item
    const currentUser = await AsyncStorage.getItem('@MyApp_user')
    console.log(currentUser)
    // console.log result:
    // {
    //   name: 'Bilal',
    //   age: 21,
    //   hobby: 'cars',
    //   traits: {
    //     eyes: 'green',
    //     hair: 'black'
    //   }
    // }
  }catch(e){}
}

  return (
    <View style={styles.container}>
      <View style={{ padding: 20 , width:'100%'}}>
        <Button title='Set Data' onPress={setData} ></Button>
      </View>

      <View style={{ padding: 20 , width:'100%'}}>
        <Button title='Show Data' onPress={showData} ></Button>
      </View>

      <View style={{ padding: 20 , width:'100%'}}>
        <Button title='Clear Data' onPress={clearData} ></Button>
      </View>

      <View style={{ padding: 20 , width:'100%'}}>
        <Button title='remove' onPress={removeWithKey} ></Button>
      </View>
      <View style={{ padding: 20 , width:'100%'}}>
        <Button title='Get All Keys' onPress={getAllKeys} ></Button>
      </View>
      <View style={{ padding: 20 , width:'100%'}}>
        <Button title='merge' onPress={mergeUsers} ></Button>
      </View>
      {
        name?(<Text>Name: {name}</Text>): null
      }
      {
        age?(<Text>Age: {age}</Text>): null
      }
      
    </View>
  );
}

const styles = StyleSheet.create({
  container: {
   marginTop:40
  },
});
```

# expo secure Storage
### Add
```
npx expo install expo-secure-store
```

### Import
```
import * as SecureStore from 'expo-secure-store';
```

### Set data
```
await SecureStore.setItemAsync("dataKey", "Hi");
```

### Show Data
```
const newData = await SecureStore.getItemAsync("dataKey");
```
### Complete code for expo secure storage
```
import { Text, View, StyleSheet, TextInput, Button } from 'react-native';
import * as SecureStore from 'expo-secure-store';
import { useState } from 'react';

async function save(key, value) {
  await SecureStore.setItemAsync(key, value);
}

async function getValueFor(key) {
  let result = await SecureStore.getItemAsync(key);
  if (result) {
    alert("Here's your value \n" + result);
  } else {
    alert('No values stored under that key.');
  }
}

export default function App() {
  const [key, onChangeKey] = useState('Your key here');
  const [value, onChangeValue] = useState('Your value here');

  return (
    <View style={styles.container}>
      <Text style={styles.paragraph}>Save an item, and grab it later!</Text>
      {}

      <TextInput
        style={styles.textInput}
        clearTextOnFocus
        onChangeText={text => onChangeKey(text)}
        value={key}
      />
      <TextInput
        style={styles.textInput}
        clearTextOnFocus
        onChangeText={text => onChangeValue(text)}
        value={value}
      />
      {}
      <Button
        title="Save this key/value pair"
        onPress={() => {
          save(key, value);
          onChangeKey('Your key here');
          onChangeValue('Your value here');
        }}
      />
      <Text style={styles.paragraph}>Enter your key</Text>
      <TextInput
        style={styles.textInput}
        onSubmitEditing={event => {
          getValueFor(event.nativeEvent.text);
        }}
        placeholder="Enter the key for the value you want to get"
      />
    </View>
  );
}

const styles = StyleSheet.create({
  container: {
    flex: 1,
    justifyContent: 'center',
    paddingTop: 10,
    backgroundColor: '#ecf0f1',
    padding: 8,
  },
  paragraph: {
    marginTop: 34,
    margin: 24,
    fontSize: 18,
    fontWeight: 'bold',
    textAlign: 'center',
  },
  textInput: {
    height: 35,
    borderColor: 'gray',
    borderWidth: 0.5,
    padding: 4,
  },
});
```