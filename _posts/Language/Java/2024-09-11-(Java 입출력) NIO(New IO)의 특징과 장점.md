---
title:  "[Java 입출력] NIO(New IO)의 특징과 장점"
categories : Java
tag : [Java, NIO, IO, NonBlocking, Channel, Buffer]
toc: true
toc_sticky: true
author_profile: false
sidebar:
  nav: "docs"
typora-root-url: ../../..
published: true
---



**Java NIO는 JDK 4부터 도입된 새로운 입출력 API로, 기존 IO의 한계를 개선하고 더 효율적인 입출력 처리를 제공합니다.**

<br>

<br>

## IO와 NIO의 주요 차이점

**java.io는 다음과 같은 문제**

- Blocking I/O
  - 데이터 read/write 시 스레드가 BLOCKED 상태가 되어 다른 작업을 수행할 수 없습니다.
- 스트림 기반
  - 스트림 기반이기 때문에 데이터 read/write 시 데이터를 순차적으로 처리합니다.
- 양방향 통신 불가능
  - 양방향 통신이 불가능하기 때문에, 각각 read와 write에 해당하는 스트림을 정의해야 합니다.

<br>

**java.nio는 다음과 같이 해결 -> new input/output**

- Blocking I/O
  - 셀렉터(Selector)와 채널(Channel)을 활용해 스레드가 BLOCKED 상태가 되지 않고 여러 I/O 작업을 동시에 처리할 수 있는 Non-Blocking 기능을 지원합니다.
- 스트림 기반
  - 버퍼(Buffer)를 통해 데이터를 관리해, 스트림보다 빠르게 데이터를 처리할 수 있습니다.
  - 스트림 기반에서 버퍼 기반으로 변경하면서, 순차적으로 처리하던 데이터를 병렬적으로 처리할 수 있게 됩니다.
- 양방향 통신 가능
  - 하나의 버퍼를 통해 read/write 모두 가능합니다.

![Image](https://github.com/user-attachments/assets/38fd5a0e-4aa3-4bae-a078-50e6150b3dc6) 

<br>

<br>

## NIO의 핵심과 예제

[참고 문서](https://velog.io/@appti/java.nio-qrmmy51p) 이 분의 글을 보자!

java.nio는 **멀티플렉싱 기반의 I/O 방식**을 사용하며, 이는 하나의 스레드가 여러 소켓의 상태를 감시하고, 필요한 작업만 처리하도록 설계된 방식

- **Selector (셀렉터)**
  - 하나의 스레드가 여러 채널의 상태를 감시할 수 있게 해주는 도구
  - 이벤트 기반 프로그래밍을 가능하게 함
- **SelectionKey (셀렉션 키)**
  - 셀렉터와 채널 간의 관계를 나타냄
  - 채널이 셀렉터에 등록되면 SelectionKey가 생성되고, 이를 통해 채널의 상태를 확인
- **Channel (채널)**
  - 입출력을 처리하는 객체. 다양한 유형의 채널(ServerSocketChannel, SocketChannel 등)이 제공
- **Buffer (버퍼)**
  - 데이터를 임시로 저장하는 메모리 공간
  - 데이터를 읽고 쓰기 위해 버퍼를 초기화하거나 모드를 변경해야 함

<br><br>

### Echo Server 코드

```java
import java.io.IOException;
import java.net.InetSocketAddress;
import java.nio.ByteBuffer;
import java.nio.channels.*;
import java.util.Iterator;
import java.util.Set;

public class EchoServer {
    public static void main(String[] args) throws IOException {
        Selector selector = Selector.open(); // Selector 열기
        ServerSocketChannel serverSocketChannel = ServerSocketChannel.open();
        serverSocketChannel.bind(new InetSocketAddress(8080)); // 서버 소켓 채널 바인딩
        serverSocketChannel.configureBlocking(false); // Non-blocking 모드 설정
        serverSocketChannel.register(selector, SelectionKey.OP_ACCEPT); // OP_ACCEPT 이벤트 등록

        System.out.println("서버가 시작되었습니다. 포트: 8080");

        while (true) {
            selector.select(); // 이벤트가 발생할 때까지 대기
            Set<SelectionKey> selectedKeys = selector.selectedKeys();
            Iterator<SelectionKey> keyIterator = selectedKeys.iterator();

            while (keyIterator.hasNext()) {
                SelectionKey key = keyIterator.next();
                keyIterator.remove();

                if (key.isAcceptable()) {
                    registerClient(selector, serverSocketChannel);
                } else if (key.isReadable()) {
                    handleClientMessage(key);
                }
            }
        }
    }

    private static void registerClient(Selector selector, ServerSocketChannel serverSocketChannel) throws IOException {
        SocketChannel client = serverSocketChannel.accept(); // 클라이언트 연결 수락
        client.configureBlocking(false);
        client.register(selector, SelectionKey.OP_READ); // OP_READ 이벤트 등록
        System.out.println("클라이언트 연결: " + client.getRemoteAddress());
    }

    private static void handleClientMessage(SelectionKey key) throws IOException {
        SocketChannel client = (SocketChannel) key.channel();
        ByteBuffer buffer = ByteBuffer.allocate(256);

        int bytesRead = client.read(buffer);
        if (bytesRead == -1) {
            client.close();
            System.out.println("클라이언트 연결 종료");
            return;
        }

        buffer.flip();
        String message = new String(buffer.array(), 0, buffer.limit()).trim();
        System.out.println("클라이언트 메시지: " + message);

        if ("EXIT".equalsIgnoreCase(message)) {
            client.close();
            System.out.println("클라이언트 연결 종료");
        } else {
            buffer.flip();
            client.write(buffer); // 메시지 에코
            buffer.clear();
        }
    }
}
```

<br><br>

### Echo Client 코드

```java
import java.io.IOException;
import java.net.InetSocketAddress;
import java.nio.ByteBuffer;
import java.nio.channels.SocketChannel;
import java.util.Scanner;

public class EchoClient {
    public static void main(String[] args) throws IOException {
        SocketChannel socketChannel = SocketChannel.open(new InetSocketAddress("localhost", 8080));
        System.out.println("서버에 연결되었습니다.");

        Scanner scanner = new Scanner(System.in);
        ByteBuffer buffer = ByteBuffer.allocate(256);

        while (true) {
            System.out.print("서버에 보낼 메시지를 입력하세요 (EXIT 입력 시 종료): ");
            String message = scanner.nextLine();

            buffer.put(message.getBytes());
            buffer.flip();
            socketChannel.write(buffer); // 서버로 메시지 전송
            buffer.clear();

            if ("EXIT".equalsIgnoreCase(message)) {
                System.out.println("서버와의 연결을 종료합니다.");
                socketChannel.close();
                break;
            }

            socketChannel.read(buffer); // 서버로부터 응답 수신
            buffer.flip();
            String response = new String(buffer.array(), 0, buffer.limit()).trim();
            System.out.println("서버 응답: " + response);
            buffer.clear();
        }
    }
}
```

<br><br>

### 주요 실행 흐름

1. **서버 소켓 채널 생성 및 설정**
   - `ServerSocketChannel`을 열고, 특정 포트(예: 8080)로 바인딩
   - Non-Blocking 모드로 설정하고, `Selector`에 `OP_ACCEPT` 이벤트 타입으로 등록
2. **클라이언트 연결 요청 수락**
   - 클라이언트가 서버로 연결 요청을 보내면, 서버는 이를 감지하여 연결을 수락
   - 클라이언트와 연결된 `SocketChannel`을 `Selector`에 등록
3. **데이터 처리**
   - 서버는 무한 루프에서 `Selector`를 사용하여 준비된 채널을 확인
   - 클라이언트가 데이터를 보내면, 해당 데이터를 읽고 처리한 후 클라이언트로 다시 전송

<br>

**Echo 서버 동작 요약**

1. 클라이언트가 서버에 연결을 요청
2. 서버는 연결을 수락하고, 클라이언트의 데이터를 읽음
3. 서버는 받은 데이터를 클라이언트에게 그대로 돌려줌
4. 클라이언트가 "EXIT" 메시지를 보내면 연결을 종료

<br>

<span style="color:#777777">NIO는 대규모 네트워크 애플리케이션에서 특히 효과적이며, 높은 확장성과 성능을 제공합니다.</span>
