# Chapter 3 �ڹ� 17

## 3.1 Text ���
```java
String query """
    SELECT `EMP_ID`, `LAST_NAME` FROM `EMPLOYEE_TB`
    WHERE `CITY` = 'INDIANAPOLIS'
    ORDER BY `EMP_ID`, `LAST_NAME`;
"""
```

String query """
SELECT `EMP_ID`, `LAST_NAME` FROM `EMPLOYEE_TB`
WHERE `CITY` = 'INDIANAPOLIS'
ORDER BY `EMP_ID`, `LAST_NAME`;

- �� ���� ���ڴ� LF(\u000A) ���н� �� ���� ��Ģ���� ��ȯ�ȴ�.
- ����ó�� �ڹ� �ҽ� �ڵ��� �߰� �鿩���⸦ ����ϱ� ���� ����� �ѷ��� ������ ������ ���ŵȴ�.
- ����� ��� �̽������� �������� �ؼ��ȴ�.

## 3.2 switch ǥ����
- ������ switch�� ������ ������ �ذ��ϰ��� switch ǥ������ �߰��Ǿ���.
```java
String result = switch (day) {
    case MONDAY, FRIDAY, SUNDAY -> "It's a day off.";
    case TUESDAY                -> "It's a work day.";
    case THURSDAY               -> "Almost there.";
    case WEDNESDAY              -> "Midweek.";
    case SATURDAY, SUNDAY        -> "Weekend.";
    default                     -> "Invalid day.";
};
```
- java 14������ yield Ű���带 ���������, java 17������ ȭ��ǥ(->)�� ����Ѵ�.
- switch ǥ������ switch���� �޸� break���� �ʿ����.
- switch ǥ������ ���� ��ȯ�� �� ������, ��ȯ���� Ÿ���� switch ǥ������ Ÿ�԰� ��ġ�ؾ� �Ѵ�.

## 3.3 record
- ������ ���� ���� �𵨸��� ���� �ְ� ������ ���� ����
- �ڹ��� Ÿ�� �ý��ۿ��� �߻��� �� �ִ� ���� �ؼ�
- ���� ���α׷��� ������ ���� ��� ���� ���� ����
- Ŭ���� ��뱸 ����
- ���ڵ� ������Ʈ��� �ϴ� �Ϸ��� �������� ������ ���� �Һ��̸鼭 ������ ĳ�����.
- �� ������Ʈ�� ������ ���� �����ϴ� final �ʵ�� ���� �˻��ϴ� ������ �޼��带 �����Ѵ�.
```java
public record Point(int x, int y) { }
```
- toString(), equals(), hashCode() �޼��带 �ڵ����� �����Ѵ�.
- �� �� �޼���� invokedynamic ����� ��Ŀ������ ���� �����Ǿ�, ��Ÿ�ӿ� ����ȭ�ȴ�.
- ����ũ: https://blog.hexabrain.net/400
- ![img.png](image/img.png)
- ![img.png](image/img2.png)

## 3.3.1 ����� Ÿ����
- ����� Ÿ������ ���ڵ��� �̸��� ���ڵ��� �ǹ̸� ��Ÿ����.
- �⺻ ������ ĳ��� ���� �߰� �޼��� ���� �߰��ϰ� �Ͱų� ���� �������̽��� �����ϰ� �������� ���ڵ尡 �ƴ� �Ϲ� Ŭ������ ����ؾ� �� ���ɼ��� ����.

## 3.3.2 ����Ʈ ���ڵ� ������
- ���ڵ� Ŭ������ ����Ʈ �������� ���� �Ű������� �Ϲ������� ����ȴ�.
- �̵��� ���ڵ� Ŭ������ �Ļ��� ���� �Ű����� ������κ��� �����ȴ�.
- ����Ʈ �����ڴ� �μ��� ���޵� ���� �����ϰ� ����ȭ�ϴ� ������ �����ϸ�,
- ������ �ʱ�ȭ �ڵ�� �����Ϸ��� �ڵ����� ó���Ѵ�.
```java
public record Point(int x, int y) {
    public Point {
        if (x < 0 || y < 0) {
            throw new IllegalArgumentException();
        }
    }
}
```
- ���� ���� �����ڸ� �����ϸ�, �����ڸ� ȣ���� �� x, y ���� �������� �˻��ϰ�, ������ ��� IllegalArgumentException�� �߻���Ų��.

## 3.4 sealed Ÿ��
- sealed Ŭ������ �ڽ��� Ȯ���� �� �ִ� Ŭ������ �����Ѵ�.
```java
public sealed class Shape permits Circle, Rectangle, Square { }
```
- permits Ű���带 ����Ͽ� ���� ����Ŭ������ �����Ѵ�. (������ ���� ���� ���� �ʿ䰡 ����.)
- �̷��� Ÿ������ ���α׷����ϸ� Shape�� �߻� ������ �ν��Ͻ��� Circle, Rectangle, Square�� �ν��Ͻ��� �����ϴ�.
- �����Ϸ��� �� ������ Ȱ���� �����ϰ� ������ �����ϰ�, ����ϴ� Ŭ���̾�Ʈ�� �� ������ ������ �� ����.

sealed Ÿ���� ������ ����.
- ���� Ŭ������ ���� Ŭ���� ������ �߰� �������� ������ ���� �ִ�.
- �ν��Ͻ� ��� Ÿ�Կ� ����� ������ �������� ������ ���� �ִ�.

## 3.5 instanceof�� ���ο� ����
- instanceof �����ڴ� ���� ���� ��Ī�� �����Ѵ�.
```java
if (obj instanceof String s) {
    System.out.println(s.length());
} else {
    System.out.println("Not a string");
}
```

## 3.6 ���� ��Ī�� ������ ���
```java
var msg = switch (obj) {
    case Integer i -> "It's an integer." + i;
    case String s  -> "It's a string." + s;
    case Long l    -> "It's a long." + l;
    case null      -> "It's null.";
    default        -> "It's something else.";
};
```

- --enable-preivew �ɼ��� ����Ͽ� ������ ����� ����� �� �ִ�.

```java
public sealed interface FXOrderResponse permits FXAccepted, FXFill, FXReject, FXCancelled {
    LocalDateTime timestamp();
    long orderId();
}

var msg = switch (resp) {
    case FXAccepted a -> a.orderId() + " Accepted";
    case FXFill f && f.units() < 100 -> f.orderId() + "Small Fill";
    case FXFill f      -> f.orderId() + "Filled " + f.units();
    case FXReject r    -> r.orderId() + "Rejected " + r.reason();
    case FXCancelled c -> c.orderId() + "Cancelled ";
    case null          -> "Order is null";
};

System.out.println(msg);
```

- null safe �� �ڵ�
- FXOrderResponse�� ���� ��� ���� Ÿ���� �˻��� �� �ִ�.