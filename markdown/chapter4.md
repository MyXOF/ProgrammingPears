# 编写正确的程序

## 问题2

参见9.3节

## 问题5

证明下面的程序在输入x为正整数时能够终止

```
while x!= 1 do
  if even(x)
    x = x / 2
  else
    x = 3 * x + 1
```

未解决的问题

## 问题 6

给定一个罐子，里面有一些黑色豆子和白色豆子，旁边有一堆黑色豆子，重复下面的过程直到罐子里面只有一个豆子为止

从罐子里面取两个豆子，如果颜色相同，就将它们扔掉并放一个额外黑色豆子；如果颜色不同，黑的扔掉白的放回去

证明该过程最终会终止，留在罐子中的豆子颜色与最初的白色和黑色豆子的数量有何函数关系

假设最开始有b个黑色和w个白色

分情况讨论

1. 都是黑色，剩下(b-1, w)
2. 都是白色，剩下(b+1, w-2)
3. 一黑一白，剩下(b-1, w)

因此只会出现(b-1, w)和(b+1, w-2)，但不管哪种，总数都少了一个，因此这个过程最终会结束。而且可以发现，白色的豆子每次减少偶数个，因此，w为奇数的时候，白色剩下，偶数的时候黑色剩下

## 问题 7

二分返回查找

```
public static int binarySearchRange(int target, int[] arr, int len){
  int index = -1;
  int lower = 0, upper = len - 1;

  if(target < arr[0]) return -1;
  if(target >= arr[len-1]) return len-1;

  while(true){
    int tmp = lower + ((upper - lower) >> 1);
    if(arr[tmp] <= target && target < arr[tmp+1]){
      index = tmp;
      break;
    } else if(arr[tmp] > target){
      upper = tmp;
    } else{
      lower = tmp;
    }
  }
  return index;
}

public static void main(String[] args) {
  int[] arr = {2,3,5,7,11,13,17,19,23,29};
  System.out.println(binarySearchRange(6, arr, 10));
  System.out.println(binarySearchRange(7, arr, 10));
  System.out.println(binarySearchRange(8, arr, 10));
  System.out.println(binarySearchRange(30, arr, 10));
  System.out.println(binarySearchRange(1, arr, 10));
  System.out.println(binarySearchRange(27, arr, 10));
}
```
## 问题 8

参见9.3节
