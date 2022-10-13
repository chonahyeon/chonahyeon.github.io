---
title: 코딩 테스트 대비를 위한 백준 문제 - Convenant  
date: '2022-10-14 +0900'
description: "코딩테스트 대비 백준 tistory - covenant 문제 추천 풀기"
categories: [Study,코딩테스트]
tags: [Java,CodingTest,코딩테스트,BaekJoon]
pin: true
---

> 코드업 기초 문제풀이 100제에 이어서, BaekJoon 사이트의 문제입니다.     
> **Covenant의 코딩테스트 대비를 위한 백준 문제 추천 :<https://covenant.tistory.com/224>{:target="_blank"}**        
> 위의 링크에 있는 Part 순서대로 풀겠습니다! 시작🔥     
> Dark 모드에서는 코드가 살짝 깨져서 보이는 거 같네요! light 모드로 변경하면 안깨집니다.

```코딩테스트 언어는 Java 입니다.```

### 준비운동 PART1. 튼튼한 기본기
--- 
👉 **약수구하기** : <https://www.acmicpc.net/problem/2501>{:target="_blank"} 

```java
public class Main {
	public static void main(String[] args) {
		Scanner sc = new Scanner(System.in);
		int num = sc.nextInt();
		int k = sc.nextInt();
		int arr[] = new int[num + 1];

		for (int i = 1; i <= num; i++) {
			if (num % i == 0) {
				arr[i] = i;
			}
		}
		System.out.println(arr[k]);
		sc.close();
	}
}
``` 
👉 **이진수** : <https://www.acmicpc.net/problem/3460>{:target="_blank"} 

```java
public class Main {
	public static void main(String[] args) {
		Scanner sc = new Scanner(System.in);
		int num = sc.nextInt();
		String arr[] = new String[num];

		for (int i = 0; i < num; i++) {
			int t = sc.nextInt();
			Integer.toBinaryString(t); // 10진수 2진수 변환
			StringBuffer sb = new StringBuffer(Integer.toBinaryString(t));
			arr[i] = sb.reverse().toString();
		}

		for (String bin : arr) {
			for (int i = 0; i < bin.length(); i++) {
				if (bin.charAt(i) == '1') {
					System.out.printf("%d ", i);
				}
			}
			System.out.println();
		}
		sc.close();
	}
}
``` 
👉 **최소,최대** : <https://www.acmicpc.net/problem/10818>{:target="_blank"} 

```java
public class Main {
	public static void main(String[] args) {
		Scanner sc = new Scanner(System.in);
		int num = sc.nextInt();
		int arr[] = new int[num];

		for (int i = 0; i < num; i++) {
			arr[i] = sc.nextInt();
		}

		Arrays.sort(arr);
		System.out.printf("%d %d", arr[0], arr[num - 1]);
		sc.close();
	}
}
``` 
👉 **지능형 기차 2** : <https://www.acmicpc.net/problem/2460>{:target="_blank"} 

```java
public class Main {
	public static void main(String[] args) {
		Scanner sc = new Scanner(System.in);
		int sum = 0;
		int max = 0;

		for (int i = 0; i < 10; i++) {
			int out = sc.nextInt();
			int in = sc.nextInt();
			sum += (in - out);
			if (max < sum)
				max = sum;
		}
		System.out.println(max);
		sc.close();
	}
}
``` 
👉 **피보나치 수 5** : <https://www.acmicpc.net/problem/10870>{:target="_blank"} 

```java
public class Main {
	public static void main(String[] args) {
		Scanner sc = new Scanner(System.in);
		int num = sc.nextInt();
		System.out.print(fibo(num));
		sc.close();
	}

	static int fibo(int Num) {
		if (Num == 0)
			return 0;
		if (Num == 1)
			return 1;
		return fibo(Num - 1) + fibo(Num - 2);
	}
}
``` 
👉 **일곱 난쟁이 - 브루트 포스** : <https://www.acmicpc.net/problem/2309>{:target="_blank"} 

```java
public class Main {
	public static void main(String[] args) {
		Scanner sc = new Scanner(System.in);

		int[] who = new int[9];
		int sum = 0;
		int fake1 = 0, fake2 = 0;
		for (int i = 0; i < 9; i++) {
			who[i] = sc.nextInt();
			sum += who[i];
		}
		sc.close();
		Arrays.sort(who);
		for (int i = 0; i < 9; i++) {
			for (int j = 0; j < 9; j++) {
				if (sum - who[j] - who[i] == 100) {
					fake1 = who[j];
					fake2 = who[i];
				}
			}
		}

		for (int a : who) {
			if (a == fake1 || a == fake2)
				continue;
			else
				System.out.println(a);
		}
	}
}
``` 
👉 **최대공약수와 최소공배수** : <https://www.acmicpc.net/problem/2609>{:target="_blank"} 

```java
// 유클리드 호제법? 2개의 자연수(또는 정식) a, b에 대해서 a를 b로 나눈 나머지를 r이라 하면
// (단, a>b), a와 b의 최대공약수는 b와 r의 최대공약수와 같다. 
// 이 성질에 따라, b를 r로 나눈 나머지 r'를 구하고, 다시 r을 r'로 나눈 나머지를 구하는 과정을 반복하여 
// 나머지가 0이 되었을 때 나누는 수가 a와 b의 최대공약수이다.

public class Main {
	public static void main(String[] args) {
		Scanner sc = new Scanner(System.in);
		int num1 = sc.nextInt();
		int num2 = sc.nextInt();
		sc.close();
		System.out.println(GCD(num1, num2));
		System.out.println(LCM(num1, num2));
	}

	static int GCD(int num1, int num2) { // 최대공약수(Greatest Common Divisor)
		if (num1 % num2 == 0) {
			return num2;
		}
		return GCD(num2, num1 % num2);
	}

	static int LCM(int num1, int num2) { // 최소공배수 (Least Common Multiple)
		return num1 * num2 / GCD(num1, num2);
	}
}
``` 
👉 **N번째 큰수** : <https://www.acmicpc.net/problem/2693>{:target="_blank"} 

```java
public class Main {
	public static void main(String[] args) {
		Scanner sc = new Scanner(System.in);
		int n = sc.nextInt();
		int[] arr = new int[10];

		for (int i = 0; i < n; i++) {
			for (int j = 0; j < 10; j++) {
				arr[j] = sc.nextInt();
				if (j == 9) {
					Arrays.sort(arr);
					System.out.println(arr[7]);
				}
			}
		}
		sc.close();
	}
}
```
👉 **소수 찾기** : <https://www.acmicpc.net/problem/1978>{:target="_blank"} 

```java
public class Main {
	public static void main(String[] args) {
		Scanner sc = new Scanner(System.in);
		int n = sc.nextInt();
		int count = 0;
		for (int i = 0; i < n; i++) {
			int num = sc.nextInt();
			if (PrimeNumber(num) != false)
				count++;
		}
		sc.close();
		System.out.println(count);
	}

	static boolean PrimeNumber(int num) {
		if (num < 2) {
			return false; // 1은 소수가 될 수 없음.
		}

		for (int i = 2; i < num; i++) {
			if (num % i == 0) {
				return false; // 소수는 1과 자기자신만을 약수로 가짐
			}
		}
		return true;
	}
}
```
👉 **쉽게 푸는 문제** : <https://www.acmicpc.net/problem/1292>{:target="_blank"} 

```java
public class Main {
	public static void main(String[] args) {
		Scanner sc = new Scanner(System.in);
		int start = sc.nextInt();
		int end = sc.nextInt();
		ArrayList<Integer> arr = new ArrayList<>();

		for (int i = 0; i < 1000; i++) {
			for (int j = 0; j < i; j++) {
				arr.add(i); // 배열 만들기 (i값을 j만큼 넣기)
			}
		}
		sc.close();
		int sum = 0;
		for (int i = start - 1; i < end; i++) {
			sum += arr.get(i);
		}
		System.out.println(sum);
	}
}
```
👉 **소수** : <https://www.acmicpc.net/problem/2581>{:target="_blank"} 

```java
public class Main {
	public static void main(String[] args) {
		Scanner sc = new Scanner(System.in);
		int m = sc.nextInt();
		int n = sc.nextInt();
		int sum = 0;
		int min = 100000;

		for (int i = m; i <= n; i++) {
			if (PrimeNumber(i) == true) {
				sum += i;
				if (i < min)
					min = i;
			}
		}
		if (sum == 0) {
			System.out.println(-1);
		} else {
			System.out.println(sum);
			System.out.println(min);
		}
		sc.close();

	}

	static boolean PrimeNumber(int num) {
		if (num < 2) {
			return false;
		}

		for (int i = 2; i < num; i++) {
			if (num % i == 0)
				return false;
		}

		return true;
	}
}
```
### 준비운동 PART2. 약점 체크
---
👉 **연산자 끼워넣기** : <https://www.acmicpc.net/problem/14888>{:target="_blank"} 

```java
public class Main {
	public static void main(String[] args) {
		Scanner sc = new Scanner(System.in);
		int m = sc.nextInt();
		int n = sc.nextInt();
		int sum = 0;
		int min = 100000;

		for (int i = m; i <= n; i++) {
			if (PrimeNumber(i) == true) {
				sum += i;
				if (i < min)
					min = i;
			}
		}
		if (sum == 0) {
			System.out.println(-1);
		} else {
			System.out.println(sum);
			System.out.println(min);
		}
		sc.close();

	}

	static boolean PrimeNumber(int num) {
		if (num < 2) {
			return false;
		}

		for (int i = 2; i < num; i++) {
			if (num % i == 0)
				return false;
		}

		return true;
	}
}
```

