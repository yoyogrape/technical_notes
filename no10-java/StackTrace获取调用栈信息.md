## 可以打印调用栈信息

工具方法一，
``` 
public static String showAllElementsInfo() {
    StringBuilder print = new StringBuilder();
    int count = 0;
    //从当前线程中获取所有的StackTraceElement
    StackTraceElement[] stackTraceElements = Thread.currentThread().getStackTrace();
    for (StackTraceElement stackTraceElement : stackTraceElements) {
        count++;
        print.append(String.format("ClassName:%s " +
                        "\nMethodName:%s " +
                        "\nMethodLine:%d " +
                        "\n当前是第%d个 " +
                        "\n---------------------------- " +
                        "\n",
                stackTraceElement.getClassName(),
                stackTraceElement.getMethodName(),
                stackTraceElement.getLineNumber(),
                count));
    }
    return print.toString();
}
```

工具方法二，
``` 
StackTraceElement stackTraceElements[] = Thread.currentThread().getStackTrace();
for (int i = 0; i < stackTraceElements.length; i++) {
    String threadName = Thread.currentThread().getName();
    long threadID = Thread.currentThread().getId();
    StackTraceElement stackTraceElement = stackTraceElements[i];
    String className = stackTraceElement.getClassName();
    String methodName = stackTraceElement.getMethodName();
    String fileName = stackTraceElement.getFileName();
    int lineNumber = stackTraceElement.getLineNumber();
    System.out.println("StackTraceElement数组下标 i=" + i + ",threadID=" + threadID + ",threadName=" + threadName + ",fileName="
            + fileName + ",className=" + className + ",methodName=" + methodName + ",lineNumber=" + lineNumber);
    System.out.println("-------------");
}
```

