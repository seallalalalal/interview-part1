# interview-part1# 國泰網銀前端面試題目 part 1

## Q1
(1)
```javascript
function sortUserName(users) {
    // Sort the users array
    users.sort((a, b) => {
    const keyA = a.firstName + a.lastName + a.customerID;
    const keyB = b.firstName + b.lastName + b.customerID;

    // Return comparison result
    if (keyA < keyB) return -1;
    if (keyA > keyB) return 1;
    return 0;
});

console.log(users);
}
```
(2)
```javascript
function sortByType(users) {
    const professionOrder = {
    'systemAnalytics': 5,
    'engineer': 4,
    'productOwner': 3,
    'freelancer': 2,
    'student': 1
    };

    users.sort((a, b) => {
    return professionOrder[b.profession] - professionOrder[a.profession];
    });

    console.log(users);
}
```

## Q2

#### (1) why doesn't it work
css的權重大小：!important > inline style > ID > Class > Element > *

權重的四個數字分別為：inline style, ID, (class / pseudo calss / attribute), element

```css
/** 權重: 0-0-3-1 **/
.container .shop-list li.item {
    color: green;
}

/** 權重: 0-0-3-0 **/
.container .shop-list .item {
    color: blue;
}
```
green的權重0-0-3-1大於blue的權重0-0-3-0，所以會優先顯示綠色字體

#### (2) how to fix it
1. 增加blue權重
```css
/** 現在權重0-0-3-1，並且因為blue的順序在green的後面，所以權重一樣的情況下blue會覆蓋green**/
.container .shop-list li.item {
  color: blue;
}
```
2. `!important` (最好不要亂用，會悲劇)
```css
/** 現在權重0-0-3-1，並且因為blue的順序在green的後面，所以權重一樣的情況下後面會覆蓋前面**/
.container .shop-list .item {
  color: blue !important;
}
```
3. 減少green的權重
```css
/** 權重0-0-3-0，並且因為blue的順序在green的後面，所以權重一樣的情況下blue會覆蓋green**/
.container .shop-list .item {
  color: green;
}
```


#### (3) styling make every other line give background color to next one
```css
.container .shop-list li:nth-child(odd) {
    background-color: lightgrey; /* Light grey for odd lines */
}

.container .shop-list li:nth-child(even) {
    background-color: lightgoldenrodyellow; /* White for even lines */
}
```
## Q3
```javascript
function getUniqueNumber(items) {
    const uniqueValues = [...new Set(items)];
    console.log(uniqueValues);
    
    return uniqueValues;
}
```

## Q4
#### Interface
Interface通常被用來定義object的型別，主要是用來確保type-checking
```javascript

interface User {
    id: number;
    name: string;
    login: () => void;
}

const userA: User = {
    id: 1,
    name: "Jane Doe",
    login: () {
        console.log(`${this.name} has login.`);
    }
    // interface可以增加除了原本定義以外的值
    email: "userA@gmail.com"
}
```

#### Enum
Enum通常被用來定義常數，尤其常用在需要做一系列的if else判斷時，這一系列的判斷條件就可以放到enum，可以很好的提升程式碼的可讀性
```typescript
enum ResponseCode {
    SUCCESS = 1,
    ERROR = 0,
}
enum ResponseMessageCodes {
    MISSING_PROPERTIES = "0001",
    TOKEN_EXPIRE = "0002",
    COURSE_ALREADY_TAKEN = "0003",
}

interface ErrorType {
    code = ResponseCode,
    message = ResponseMessageCodes,
}

try {
  // do some http request here
} catch (error: ErrorType) {
  switch (error.message) {
    case ResponseMessageCodes.MISSING_PROPERTIES:
        // do somthing
        break;
    case ResponseMessageCodes.TOKEN_EXPIRE:
        // do somthing
        break;
    case ResponseMessageCodes.COURSE_ALREADY_TAKEN:
        // do somthing
        break;
    default:
        // throw error exception
        break;
  }
}
```

## Q5
setState是一個async執行的function，前一行的setState還沒執行完count還沒更新到最新的數值，下一行setState就被執行了
```javascript
// count: 0 (initial state)
handleAddCount() {
  this.setState({ count: this.state.count + 1 }); // count: 1
  this.setState({ count: this.state.count + 1 }); // count: 1 (still based on initial state)
  this.setState({ count: this.state.count + 1 }); // count: 1 (again based on initial state)
}
```
正確的做法：
```javascript
// 1. 使用prevState，只要是對state做運算最好永遠都要用prevState比較不會出錯
handleAddCount() {
  this.setState((prevState) => ({ count: prevState.count + 1 }));
  this.setState((prevState) => ({ count: prevState.count + 1 }));
  this.setState((prevState) => ({ count: prevState.count + 1 }));
}
// 2. 把三次呼叫合成一次
handleAddCount() {
  this.setState((prevState) => ({ count: prevState.count + 3 }));
}
```

## Q6
```javascript
var SearchBox = React.createClass({
    debounceTimer: null,

    handleOnChange: function (event) {
        // Clear the previous timer
        clearTimeout(this.debounceTimer);

        // Set a new timer
        this.debounceTimer = setTimeout(() => {
            //AJAX call
             console.debug(event.target.value);
        }, 500);
    },

    render: function () {
        return <input type="search" name="p" onChange={this.handleOnChange} />;
    },
});
```
