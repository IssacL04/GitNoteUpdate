## Reflection R6B——List Recursion

![](https://cdn.jsdelivr.net/gh/IssacL04/IHS@img/img/202404151157439.png)

![](https://cdn.jsdelivr.net/gh/IssacL04/IHS@img/img/202404151157440.png)

![](https://cdn.jsdelivr.net/gh/IssacL04/IHS@img/img/202404151157441.png)

```js
//Flatten lists
function flatten_list(lst) {
    function append(xs,ys){
        return is_null(xs)
            ? ys
            : pair(head(xs),append(tail,ys));
    }
    function accumulate(op, initial ,xs){
        return is_null(xs)
            ?initial
            :op(head(xs),accumulate(op,initial,tail(xs)));
    }
    return accumulate(append,null,lst)
}
//Tree sums
function tree_sums(tree){
    if (is_null(tree)) {
        return 0;
    } 
    else if (is_list(head(xs))) {
        return tree_sum(head(tree))+tree_sum(tail(tree));
    } 
    else {
        return head(xs)+tree_sum(tail(xs));
    }
}
//accumulate_tree
/*
function accumulate_tree(f1, f2, initial, tree) {
    if (is_null(tree)) {
        return initial;
    } 
    else if (is_list(tree)) {
        const head = accumulate_tree(f1, f2, initial,head(tree));
        const tail = accumulate_tree(f1, f2, initial,tail(tree));
        return f2(head, tail);
    } 
    else {
        return f1(tree);
    }
}
*/
//Standard version
function accumulate_tree(f1, f2, initial, tree) {
    if (is_null(tree)) {
        return initial;
    } 
    else if (is_list(head(tree)) {
        return f2(accumulate_tree(f1,f2,initial,head(xs)),tail(xs));
    } 
    else {
        return f2(f1(head(xs)),acuumulate_tree(f1,f2,tail,tail(xs)));
    }
}
//Function map using accumulate
```

## Reflection R7A——List Recursion

![](https://cdn.jsdelivr.net/gh/IssacL04/IHS@img/img/202404151157442.png)

```js
function my_map(f,xs){
    return accumulate(x => x+1，0,xs);
}
```

```js
function appear(x,lst){
    if(is_null(lst)){
        return false;
    }else if(x===head(lst)){
        return true;
    }else{
        return appear(x,tail(lst));
    }
}
function remove_duplicates(lst){
    return accumulate(pair(y,filter(a=>a!==y,ys)),null,xs);
}
```







```js
function make_withdraw(balance, password) {
  let attempts = 0;
  let disabled = false;

  function withdraw(amount, enteredPassword) {
    if (disabled) {
      return "Account disabled";
    }
    if(enteredPassword!==password){
        attempts = attempts+1;
        if(attempts===3){
            disabled = true;
            return "Account disabled";
        }
        return "Wrong password; no withdraw";
    }
    else{
        if(balance >=amount){
            balance =balance-amount;
            return balance;
        }
        else{
            return "Insufficient funds";
        }
    }
  }
  return withdraw;
}
const acc = make_withdraw(100,"ABC");
acc(30,"ABCD");

 
```





```js
let commission = 25; // my commission in dollars

// return a calculator for total price
// total price = (commission + cost) * (1 + tax_rate)

function make_price_calculator(tax_rate) {
    function calculator(cost) {
        return (commission + cost) * (1 + tax_rate);
    }
    return calculator;
}

const calc = make_price_calculator(0.07);
commission = 125;
calc(75);
```

![diagram_0_0](C:\Users\27269\Downloads\diagram_0_0.jpg)

## Reflection R9A——Matrix Rotation

![](https://cdn.jsdelivr.net/gh/IssacL04/IHS@img/img/202404151157443.png)

```js
function swap(M, x, y,m,n) {
 let temp = M[x][y];
 M[x][y] = M[m][n];
 M[m][n] = temp;
}
function rotate_Matrix(M){
    const n = array_length(M);
    //Center Symmetrical transformation
    for(let i=0;i<n;i=i+1){
        for(let j=i+1;j<n;j=j+1){
            swap(M,i,j,j,i);
        }
    }
    //Symmetrical transformation with the axis array_length/2
    for(let i=0;i<n;i=i+1){
        for(let j=0;j<n/2;j=j+1){
            swap(M,i,j,i, n - j - 1);
        }
    }
    return M;
}
const matrix = [
  [1, 2, 3, 4],
  [5, 6, 7, 8],
  [9, 10, 11, 12],
  [13, 14, 15, 16]
];

rotate_Matrix(matrix);

//OUTPUT:[[13, 9, 5, 1], [14, 10, 6, 2], [15, 11, 7, 3], [16, 12, 8, 4]]
```

