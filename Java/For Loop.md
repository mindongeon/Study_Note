# for 루프의 성능 향상

switch문은 JDK 6까지는 byte, short, char, int 네 가지 타입을 사용한 조건 분기만 가능했다.
JDK 7부터는 String도 사용이 가능하다.   
일반적으로 if문에서 분기를 많이하면 시간이 많이 소요된다고 생각한다.
if문 조건 안에 들어가는 비교 구문에서 속도를 잡아먹지 않는한,
if문장 자체에서는 그리 많은 시간이 소요되지 않는다.

# 반복 구문에서의 속도

JDK 5.0 이전에 for 구문은 다음과 같다.

```java
for(int loop=0;loop<list.size();loop++)
```

이렇게 사용을 하게되면 `list.size()` 메서드를 매번 호출해야해 좋지않다.
이럴 경우 다음과 같이 수정한다.

```java
int listSize=list.size();
        for(int loop=0;loop<listSize; loop++)
```

이렇게 사용하면 `list.size()`의 반복 호출이 없어지게 되어 더 빠른 처리가 가능하다.
JDK 5.0부터는 **for-each**를 사용할 수 있다.

```java
ArrayList<String> list=new ArrayList<String>();
        ...
        for(String str:list)
```

성능을 측정해보면 자바8의 forEach를 사용한게 가장 빠르게 나오고,
일반 for-each가 가장 느리게 나온다.

# 반복 구문에서의 필요 없는 반복

가장 많은 실수 중 하나는 반복 구문에서 계속 필요없는 메서드를 호출하는 것이다.

```java
public class Sample1 {
    public void sample(DataVo data, String key) {
        TreeSet treeSet2 = null;
        treeSet2 = (TreeSet) data.get(key);
        if (treeSet2 != null) {
            for (int i = 0; i < treeSet2.size(); i++) {
                DataVo2 data2 = (DataVo2) treeSet2.toArray()[i];
            }
        }
    }
}
```

TreeSet 형태의 데이터를 갖고 있는 DataVo에서 TreeSet을 하나 추출하여 처리하는 부분이다.
이 소스의 문제는 `toArray()` 메서드를 반복해서 수행한다는 것이다.
수정을 하게 되면 다음과 같다.

```java
public class Sample1 {
    public void sample(DataVo data, String key) {
        TreeSet treeSet2 = null;
        treeSet2 = (TreeSet) data.get(key);
        if (treeSet2 != null) {
            DataVO2[] dataVO2 = (DataVO2) treeSet2.toArray();
            int treeSetSize = treeSet2.size();
            for (int i = 0; i < treeSetSize; i++) {
                DataVO2 data2 = dataVO2[i];
            }
        }
    }
}
```

# 출처

[지금까지 사용하던 for 루프를 더 빠르게 할 수 있다고?](https://yangbongsoo.gitbook.io/study/undefined/for)