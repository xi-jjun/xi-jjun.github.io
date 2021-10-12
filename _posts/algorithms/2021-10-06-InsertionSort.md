---
layout: post
title: "Insertion Sort"
author: "xi-jjun"
tags: Algorithm

---

# Algorithm Tag

알고리즘에 대해 공부하기 위한 태그이다.

<br>

# Insertion Sort

- 개념 - 현재 오름차순 정렬
  1. 정렬할 배열에는 2개의 그룹이 존재한다고 생각. Sorted(==S) 그룹, UnSorted(==U) 그룹.
  2. 무조건 맨 첫 index의 배열 값은 S그룹으로 들어가고, 1번째 index부터 마지막 index까지의 원소는 U그룹이다.
  3. U그룹의 맨 첫 원소를 S그룹의 마지막 원소부터 첫번째 원소까지 비교를 시작한다.
     1. 만약 S그룹의 원소보다 크면, S그룹으로 원소를 보낸 뒤 비교 멈추고 U그룹의 다음 비교 대상으로.
     2. 만약 S그룹의 원소보다 작으면, S그룹의 원소의 index를 +1. 그 후 S그룹의 다음 원소와 현재 U그룹의 원소를 비교.

말로 풀어서 글을 쓰는 것 보단 직접 구현을 해보는게 빠를 것 같다.

<br>

## Step by step

![insertion1](https://github.com/xi-jjun/xi-jjun.github.io/blob/master/_posts/algorithms/img/insertion1.png?raw=True){: width="70%"}

첫 단계에서는 위와 같이 시작한다. 현재 2와 10을 비교해야 하고, U그룹의 `10`은 S그룹의 `2`보다 크기 때문에 `10`을 S그룹으로 보낸 뒤 비교 멈춘다. 

Insertion sort의 핵심은, 적어도 S그룹에서의 배열은 **정렬되었다는 것**이다.

<br>

![insertion1](https://github.com/xi-jjun/xi-jjun.github.io/blob/master/_posts/algorithms/img/insertion1.png?raw=True){: width="70%"}

2번째 단계이다. 

- `2-1` : 1과 10을 비교. U그룹의 1이 더 작으므로 10의 index를 +1.
- `2-2` : 1과 S그룹의 그 다음 원소인 2를 비교. U그룹의 1이 더 작으므로 2의 index를 +1.
- `2-3` : 더 이상 비교할 수 있는 S그룹의 원소가 없으므로 비교 멈춤. 해당 index에 1이 Insertion됨.

...

이와 같이 U그룹의 모든 원소에 대해 반복을 해준다면 배열은 오름차순으로 깔끔하게 정렬이 될 것이다.

<br>

## Time complexity

- 최선일 경우 - `O(N)`

  비교를 할 때에 항상 U그룹의 원소가 S그룹의 마지막 원소보다 값이 커서 data의 이동이 없을 경우이다.

  더 쉽게 말하자면, **배열이 이미 정렬되어있을 때** 라고도 볼 수 있다. 따라서 Insertion sort는 거의 정렬이 되어 있는 경우에는 매우 효율적일 수 있다.

- 최악의 경우 - `O(N*N)`

  *현재 우리는 배열을 `오름차순` 정렬을 하려는 것이다.*

  만약 배열이 내림차순으로 정렬이 되어 있다면...? U그룹의 모든 원소들은 매번 S그룹의 모든 원소들과 비교를 당하고, S그룹의 모든 원소들은 U그룹과 비교를 시행할 때마다 항상 위치가 바뀐다. 따라서 원소의 개수가 N인 경우에서 `for loop`는 `(N-1) + (N-2) + ... + 2 + 1 = N(N-1) / 2`번의 연산을 수행한다.(현재 worst case 가정)

<br>

Selection sort와 비교해서 Best case인 경우에는 더 효율적이지만, 데이터가 많아질 때 O(N<sup>2</sup>)의 time complexity로써 2개의 알고리즘은 비효율적이다.

<br>

## 구현

```java
import java.io.*;
import java.util.StringTokenizer;

// 3 4 2 5 6 1
public class InsertionSort {
    static void insertionSort(int[] arr) {
        for (int i = 1; i < arr.length; i++) { // N개의 data에 대해 탐색.
            int key = arr[i]; // U그룹의 첫번째 원소를 key 값으로 지정.

            for (int k = i - 1; k >= 0; k--) { // S그룹의 마지막 원소부터 첫번째 원소로 비교 시작.
                if (key < arr[k]) arr[k + 1] = arr[k]; // U그룹의 원소인 key가 현재 비교할 S그룹의 원소보다 작으면 S그룹의 원소 index를 +1
                else { // U그룹의 원소인 key가 S그룹의 원소보다 크다면, 즉시 비교를 멈추고 해당 index에 key값 insertion!
                    arr[k + 1] = key; // key값 insertion!
                    break; // 즉시 비교 멈춤.
                }
            }
        }
    }
}

```

위와 같이 코드를 구현할 수 있다.
