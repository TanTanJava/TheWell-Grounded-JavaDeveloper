# Chapter 2

## 2.1 ��� ����
- ����� ��Ÿ�ӿ� �ǹ̸� ������ ���� ���α׷� ���� �� �������� �����̴�.
- ����� Ư¡
  - ����� ��� ���� ������ ������ �����ϹǷ� ������ �Ǵ� ���ø����̼� ���� �������� ��� ������ �ذ�� ���� ������ ������ �� �ִ�.
  - ������ ĸ��ȭ�� �����ؼ� ���� ��Ű���� Ŭ������ �����Ϸ��� ����ڷκ��� �����ϰ� ��ȣ�� �� �ִ�.
  - �ֽ� �ڹ� ��Ÿ�ӿ��� �����ϰ� ����� �� �ִ� ��Ÿ�����Ͱ� ���Ե� ������ ���� �����̸�, �ڹ� Ÿ�� �ý��ۿ���(ex: ���÷���) ǥ���ȴ�.

### 2.1.1 ������Ʈ ����
- ������ �ƴ� ���� �ڹ� ��Ÿ��
- ������ ĸ��ȭ
- ���� JVM
  - ���� ������ ��� �̸����ε� ���� �����ϰ� �ٲ�

### 2.1.2 ��� �׷���
- ���ȭ�� �ٽ��� ����� ���� �����ϴ� ����� ��Ÿ���� ��� �׷����̴�.
- ��� �׷����� ���� ���ȯ �׷������� �ϱ� ������ ��ȯ �������� ���� �� ����.

### 2.1.3 ���� ��ȣ�ϱ�
- ����� �ذ��ؾ��� �ֿ� ���� �� �ϳ��� �ڹ� �����ӿ�ũ�� ���� ������ ���� ������ �����ϰ� ���յ� �ִٴ� ���̴�.
- �ڵ�� ���� Ŭ������ ���� �׼����ؼ��� �ȵȴ�.

### 2.1.4 ���ο� ���� ��� ���� �ǹ�
- ������Ʈ�� ���� ���ο��� ȣ��Ǵ� API�� �ܺο��� ȣ��Ǵ� API�� ���� �����Ͽ� Ŭ������ ���� �޼��尡 ��� �ڵ忡 �ڵ����� ǥ���Ǵ� ���� ���´�.

## 2.2 �⺻���� ��� ����
```java
module wgjd.discovery {
    exports wgjd.discovery;
    
    requires java.instrument;
    requires java.attach;
    requires java.internal.jvmstat;
}
```
- module�� �ܼ� ������ �ǹ��Ѵ�.
- ��� �̸��� ���� ��Ģ
  - ����� �۷ι� ���ӽ����̽��� �ִ�.
  - ��� �̸��� �����ؾ� �Ѵ�.
  - �����ϸ� ǥ�������� com.company.project�� ����Ѵ�.

## 2.5 ����� ���� ��Ű��ó
- ���� ���� ��ȯ�ؾ��ϴ� �ʿ伺�� ����.

## 2.6 ����� �Ѿ
- ����� �ٽ��� �ڹ� �� ������ �߻�ȭ, �� �ҽ� �ڵ� �����Ϸ��� ��Ÿ�ӿ��� �ŷ��� �� �ְ� �������� �����ϴ� ���� ������� ������ �����ϴ� ���̴�.
- JLink�� ���� ����
  - ������ ���ȭ�� �������� ���� ���ø����̼ǿ����� �۵��Ѵ�.
  - ����ȭ �ڵ�ʹ� ȣȯ���� �ʴ´�.
  - �ڵ� ��⸸���δ� ������� �ʴ�.