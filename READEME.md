## 分布式Linux性能分析监控系统  

**本系统采用C++技术栈，并结合 Docker、gRPC、Protocol Buffers等工具和框架的分布式Linux性能分析监控系统。通过模块化的设计，该项目旨在实现对分布式Linux 系统的性能监控和分析。**

- **docker模块：用Dockerfile指定相应的CMake、gRPC、proto等源码和依赖，构建整个项目环境。编写容器操作脚本指令，用于开启容器并进入docker运行环境。**
- **monitor模块：采用工厂模式，通过构造Monitor的抽象类定义接口。每3s监控一次/proc目录下的系统“运行时”信息，包括：CPU最近1/5/15分钟的平均负载、CPU状态信息、CPU平均使用率、CPU软中断信息、内存使用信息和网络流入流出统计信息。并为了模拟出真实的性能问题，使用stress工具进行模拟压测，分析相应时刻服务器的cpu状况和中断情况。由于代码的低耦合特性，后续可以扩展更多系统监控。(C++)**
- **gRPC模块：通过gRPC框架，构建出相应的Server与Client。Server在所要监控的服务器部署，负责信息采集；Client端用来监控和展示。并为了降低耦合性，项目每个模块相互独立，只通过调用gRPC服务来进行远程连接。**
- **protobuf模块：使用protobuf序列化协议，构建出整个项目的数据结构。**

### <u>应用技术</u>

 C++、Docker、gRPC、protobuf、Cmake、工厂方法。

### 主要工作

1、构建Docker模块：构建一个容器，包含CMake、gRPC、Protocol Buffers等多个依赖，以便在多台服务器上部署环境。  
2、构建Monitor模块：实现灵活的监控接口，能够有效地捕获CPU状态、系统负载、软中断、内存、网络等性能指标，同时还需要模拟真实性能问题。  
3、调用gRPC框架：构建Server和Client，实现模块之间的远程连接，同时降低模块间的耦合性。  
4、Protocol Buffers序列化：构建整个项目的数据结构，并确保高效的数据交换。  

## Linux 系统性能相关的信息在哪？

在 Linux 系统中，性能相关的信息通常并不存储在一个单一的文件里，而是分散在多个系统文件和目录中，这些文件和目录包含了关于 CPU、内存、磁盘、网络等各个方面的详细数据。Linux 提供了一些标准化的文件和工具，供用户或程序查询系统的性能数据。

### 主要的性能相关信息来源

1. **/proc 文件系统**： `/proc` 是一个虚拟文件系统，它提供了关于当前系统状态的实时信息，包括硬件、内核和进程的各种信息。以下是一些常见的与性能相关的文件：
   - **/proc/cpuinfo**：包含关于 CPU 的详细信息，如型号、频率、核心数等。
   - **/proc/meminfo**：显示内存使用情况，包括总内存、可用内存、缓存等。
   - **/proc/loadavg**：显示系统的负载平均值，反映系统负荷情况。
   - **/proc/uptime**：显示系统的启动时间和空闲时间。
   - **/proc/stat**：包含各种系统统计信息，包括 CPU 使用情况、上下文切换、进程调度等。
   - **/proc/net/dev**：显示网络接口的相关统计信息，包括发送和接收的数据包数、错误数等。
   - **/proc/diskstats**：显示磁盘的读写操作统计信息。
2. **/sys 文件系统**： `/sys` 也是一个虚拟文件系统，提供与内核相关的配置信息。它主要用于控制和配置内核参数，但也可以用于查看硬件性能数据：
   - **/sys/class/net/**：显示网络设备的状态信息，可以查看网络接口的详细信息。
   - **/sys/block/**：显示块设备的详细信息，如磁盘、分区等。
3. **/var/log 目录**： 在 Linux 中，日志文件通常存储在 `/var/log/` 目录下。这些日志文件中记录了与系统性能、硬件、网络等相关的事件和错误信息。例如：
   - **/var/log/syslog** 或 **/var/log/messages**：系统日志文件，记录了系统的事件和错误信息。
   - **/var/log/dmesg**：内核日志文件，记录了系统启动时的硬件检测和内核信息。
   - **/var/log/kern.log**：记录内核相关的日志信息，可能包含与系统性能相关的错误和警告。
4. **netstat / ss 命令**： 这些命令可以显示与网络性能相关的信息，包括当前打开的网络连接、传输的数据量、网络接口的统计数据等。
5. **top / htop 命令**： 这些命令实时显示系统资源的使用情况，包括 CPU、内存、进程等的使用情况。
6. **iostat、vmstat、mpstat 等命令**：
   - **iostat**：显示 CPU 使用情况和磁盘 I/O 性能。
   - **vmstat**：显示系统的虚拟内存、进程、CPU、磁盘等性能信息。
   - **mpstat**：显示每个 CPU 的使用情况。

### 如何通过这些文件获取性能信息？

Linux 系统通过 `/proc` 和 `/sys` 文件系统，提供了直接读取系统性能信息的方式。这些文件和目录提供了有关 CPU、内存、磁盘、网络等方面的实时数据。通过读取这些文件，可以获取系统的性能数据，并进行分析。

例如：

- 查看系统的内存使用情况：

  ```
  cat /proc/meminfo
  ```

- 查看系统的负载平均值：

  ```
  cat /proc/loadavg
  ```

- 查看 CPU 使用情况：

  ```
  cat /proc/stat
  ```

通过这些文件，系统管理员或应用程序可以实时获取系统的各项性能数据，并做出相应的调优或处理。





### Docker

Docker 是一种**开源的容器化平台**，能够将应用程序及其依赖打包到一个轻量级的可移植容器中，并且能够在任何支持 Docker 的操作系统上运行。这使得应用程序可以实现 "一次构建，到处运行" 的目标。

它本质上是一种**容器技术**，通过将应用程序及其运行所需的环境打包在一起，提供了一种一致性强、可移植的开发和部署解决方案。

Docker 是一个用于自动化应用程序部署、扩展和管理的开源平台。它使用容器化技术，使得开发者能够在任何地方（无论是本地开发环境、测试环境还是生产环境）以一致的方式运行应用程序。

### 通俗理解 Docker

想象你有一个应用程序，通常它在你的开发电脑上可以正常运行，但在部署到服务器或让其他人使用时，它可能会因为环境配置不一致而出现问题，比如缺少库文件、不同版本的操作系统等。

Docker 就像是一个“盒子”，把你所有需要的文件、依赖、环境设置都打包到一起，不管你在哪里运行它，它都会以相同的方式工作，就像是在“隔离的环境”中运行。这样，你可以确保应用无论是在开发环境、测试环境还是生产环境都能稳定运行。



### **Docker 的核心概念**

Docker 的工作原理和功能围绕几个核心概念展开：

#### 1. **镜像（Image）**

- Docker 镜像是一个只读的模板，包含运行应用程序的必要文件和配置（如操作系统、依赖库、应用代码）。
- 镜像是创建 Docker 容器的基础。
- 类似于虚拟机的快照，但比虚拟机更轻量。
- 可以从 Docker Hub 拉取现成的镜像，也可以根据自己的需要创建自定义镜像。

#### 2. **容器（Container）**

- Docker 容器是镜像的一个实例，是运行时的环境。
- 容器是一种轻量级的沙盒，包含运行应用程序所需的所有文件。
- 容器与主机共享操作系统内核，但有自己的文件系统、网络和资源隔离。
- 容器可以**启动、停止、删除、迁移**，其生命周期独立于主机。

#### 3. **Dockerfile**

- Dockerfile 是一个文本文件，包含了一系列指令，用于定义如何构建 Docker 镜像。

- 开发者可以通过 Dockerfile 定义应用的环境、依赖和配置。

- 一个典型的 Dockerfile 示例：

  ```
  dockerfile复制代码# 基于官方 Python 镜像
  FROM python:3.9-slim
  # 设置工作目录
  WORKDIR /app
  # 复制项目文件到容器
  COPY . /app
  # 安装依赖
  RUN pip install -r requirements.txt
  # 定义启动命令
  CMD ["python", "app.py"]
  ```

#### 4. **Docker 引擎**

- Docker 引擎是一个运行在主机上的服务，它负责创建和管理容器。
- Docker 引擎包括：
  - **Server（守护进程 `dockerd`）**：负责处理容器相关的请求。
  - **CLI（命令行界面）**：用户与 Docker 的交互工具。
  - **REST API**：提供给开发者访问 Docker 功能的编程接口。

#### 5. **仓库（Repository）**

- 仓库是存放 Docker 镜像的地方，可以是公有的（如 Docker Hub）或私有的（如自建仓库）。
- 仓库中的镜像以版本号标识（Tag）。
- 常见命令：
  - 从仓库拉取镜像：`docker pull <镜像名>:<版本号>`
  - 推送镜像到仓库：`docker push <镜像名>:<版本号>

### **Docker 的特点**

1. **轻量级**
   - 容器共享宿主机的操作系统内核，相比虚拟机占用更少的资源。
   - 启动容器非常快，通常在几秒钟内完成。
2. **跨平台与可移植性**
   - 容器包含所有运行所需的环境，因此可以在不同平台之间轻松迁移（开发环境、测试环境、生产环境一致）。
3. **一致性**
   - 通过容器打包应用，开发和部署的环境完全一致，避免了“它在我的电脑上可以运行”的问题。
4. **高效的资源利用**
   - 容器比虚拟机更节省资源，可以在同一主机上运行更多的容器实例。
5. **模块化**
   - 可以将应用的不同服务（如数据库、缓存、Web 服务等）分别放在独立的容器中，便于管理和扩展。

------

### **Docker 的常见应用场景**

1. **应用程序的容器化**
   - 将应用程序及其依赖打包成一个 Docker 容器，便于开发、测试和部署。
2. **微服务架构**
   - 每个微服务运行在一个独立的容器中，便于部署、扩展和更新。
3. **持续集成与持续部署 (CI/CD)**
   - 将 Docker 容器与 Jenkins 等工具结合，构建自动化的 CI/CD 流程。
4. **跨平台开发**
   - 开发人员可以使用相同的容器，无需担心不同开发环境之间的差异。
5. **快速部署和扩展**
   - 使用 Docker Swarm 或 Kubernetes，可以快速部署和扩展容器化应用。
6. **沙盒环境**
   - 创建隔离的容器用于测试新技术或运行不可信的程序。

------

### **Docker 的核心组件**

#### 1. **Docker 引擎**

Docker 引擎是运行 Docker 的核心服务，分为以下几部分：

- **守护进程 (Docker Daemon)**：运行在主机上，负责构建、运行和管理容器。
- **CLI 客户端**：通过命令行与 Docker Daemon 交互。
- **REST API**：提供程序化访问 Docker 的接口。

#### 2. **镜像**

- 镜像是 Docker 容器的基础。它是一个分层文件系统，每一层都对应一个文件或修改。
- Docker 使用 Union File System 技术，只加载修改的部分，提升效率。

#### 3. **容器**

- 容器是镜像的实例化，它是一个运行时环境，包含镜像内容和运行时配置。

#### 4. **网络**

- Docker 提供多种网络模式：
  - **bridge**：默认模式，容器通过桥接网络与主机通信。
  - **host**：容器与主机共享网络栈。
  - **none**：容器完全隔离网络。
  - **overlay**：用于跨主机的容器通信（Swarm 模式下）。

#### 5. **存储**

- Docker 容器的文件系统是临时的，重启后会丢失。可以使用以下存储方式：
  - **数据卷 (Volume)**：持久化数据的推荐方式。
  - **绑定挂载 (Bind Mount)**：直接将主机目录挂载到容器中。

------

### **Docker 的基本操作**

#### 1. 安装 Docker

- 在 Ubuntu 上安装 Docker：

  ```
  bash复制代码sudo apt update
  sudo apt install docker.io
  ```

- 验证安装：

  ```
  bash
  
  
  复制代码
  docker --version
  ```

#### 2. 常用命令

- 镜像管理

  ```
  bash复制代码docker pull <image-name>       # 拉取镜像
  docker images                 # 查看本地镜像
  docker rmi <image-id>         # 删除镜像
  ```

- 容器管理

  ```
  bash复制代码docker run <image-name>       # 运行容器
  docker ps                     # 查看运行中的容器
  docker ps -a                  # 查看所有容器
  docker stop <container-id>    # 停止容器
  docker rm <container-id>      # 删除容器
  ```

- 构建镜像

  ```
  bash
  
  
  复制代码
  docker build -t <image-name> .  # 使用 Dockerfile 构建镜像
  ```

- 日志与调试

  ```
  bash复制代码docker logs <container-id>    # 查看容器日志
  docker exec -it <container-id> bash  # 进入容器终端
  ```

------

### **Docker 的优缺点**

优点：

- 容器轻量、启动快。
- 简化开发到部署的流程。
- 提高系统资源利用率。
- 强大的社区支持和生态系统。

缺点：

- 容器隔离性不如虚拟机（共享内核）。
- 学习曲线可能对新手稍有难度。
- 对 Windows 的支持不如 Linux 原生。

------

### **在分布式Linux性能分析项目中的作用**

在你的分布式Linux性能分析项目中，Docker 的作用可能包括：

1. **环境一致性**：使用 Docker 容器将系统的依赖项（如 gRPC、Protocol Buffers 等）打包，确保开发、测试、部署环境一致。
2. **快速部署**：通过 Docker 容器化部署系统，节省部署时间。
3. **模块化管理**：不同的系统模块可以运行在独立的容器中，便于更新和扩展。
4. **跨平台支持**：容器化后可以在不同的机器和操作系统上运行，而无需修改环境配置。

### gRPC 简介

gRPC（**gRPC Remote Procedure Call**）是由 Google 开发的一个高性能、开源的远程过程调用（RPC）框架，允许客户端和服务器之间通过网络进行直接的函数调用，就像调用本地函数一样。gRPC 的核心目标是提供高效、强类型和多语言的远程通信支持。

------

### gRPC 的主要特性

1. **跨语言支持**：
   - gRPC 支持多种编程语言（C++、Python、Java、Go、C#、Ruby 等），并且通过 Protocol Buffers（Protobuf）定义接口，具有跨语言的兼容性。
2. **高性能通信**：
   - gRPC 使用 **HTTP/2** 作为底层传输协议，支持多路复用、二进制数据传输和流式数据通信。
   - HTTP/2 相比传统的 HTTP/1.1，大幅减少了网络延迟，并支持同时处理多个请求。
3. **协议与序列化优化**：
   - gRPC 使用 Protocol Buffers 作为数据序列化格式，这是一种轻量、高效、跨语言的数据结构序列化方法。
   - 相比 JSON 和 XML，Protobuf 的序列化数据体积更小，速度更快，解码和编码的开销更低。
4. **双向流支持**：
   - gRPC 支持 **双向流式通信**，客户端和服务器可以在同一个连接中互相发送数据流，适用于实时通信的场景。
5. **自动生成代码**：
   - gRPC 自动根据 `.proto` 文件生成客户端和服务端代码，减少了重复性开发工作。
6. **扩展性**：
   - gRPC 提供多种拦截器、负载均衡、服务发现等功能，方便系统的扩展。

------

### gRPC 的工作原理

1. **服务定义**：

   - 使用 Protocol Buffers 定义 gRPC 服务接口和消息结构，通常写在 `.proto` 文件中。

   - 示例 

     ```
     .proto
     ```

      文件：

     ```
     proto复制代码syntax = "proto3";
     
     service Greeter {
       rpc SayHello (HelloRequest) returns (HelloReply);
     }
     
     message HelloRequest {
       string name = 1;
     }
     
     message HelloReply {
       string message = 1;
     }
     ```

2. **代码生成**：

   - 通过 gRPC 的工具链（如 `protoc`），从 `.proto` 文件生成客户端和服务端的代码。

   - 例如，生成 C++ 的客户端和服务端代码：

     ```
     bash复制代码protoc --grpc_out=. --plugin=protoc-gen-grpc=/path/to/grpc_cpp_plugin yourfile.proto
     protoc --cpp_out=. yourfile.proto
     ```

3. **客户端调用和服务端实现**：

   - 客户端调用生成的存根代码（Stub）与服务器通信，而服务端实现接口逻辑。

   - 示例服务端实现（C++）：

     ```
     cpp复制代码class GreeterServiceImpl final : public Greeter::Service {
       grpc::Status SayHello(grpc::ServerContext* context, const HelloRequest* request,
                             HelloReply* reply) override {
         reply->set_message("Hello " + request->name());
         return grpc::Status::OK;
       }
     };
     ```

   - 客户端调用示例：

     ```
     cpp复制代码auto stub = Greeter::NewStub(channel);
     HelloRequest request;
     request.set_name("World");
     HelloReply reply;
     grpc::ClientContext context;
     grpc::Status status = stub->SayHello(&context, request, &reply);
     if (status.ok()) {
       std::cout << "Reply: " << reply.message() << std::endl;
     }
     ```

4. **传输过程**：

   - 客户端调用 gRPC 方法，序列化为二进制数据（使用 Protobuf）。
   - 数据通过 HTTP/2 发送到服务端。
   - 服务端反序列化数据、执行逻辑、返回结果。
   - 客户端接收结果并反序列化。

------

### gRPC 通信模式

gRPC 提供了 4 种主要的通信模式，适配不同的应用场景：

1. **简单 RPC（Unary RPC）**：

   - 客户端发送一个请求，服务端返回一个响应。

   - 示例：

     ```
     proto
     
     
     复制代码
     rpc SayHello (HelloRequest) returns (HelloReply);
     ```

2. **服务端流式 RPC（Server Streaming RPC）**：

   - 客户端发送一个请求，服务端返回一个包含多个数据的流。

   - 示例：

     ```
     proto
     
     
     复制代码
     rpc ListFeatures (Rectangle) returns (stream Feature);
     ```

3. **客户端流式 RPC（Client Streaming RPC）**：

   - 客户端发送一个数据流，服务端处理完成后返回一个响应。

   - 示例：

     ```
     proto
     
     
     复制代码
     rpc RecordRoute (stream Point) returns (RouteSummary);
     ```

4. **双向流式 RPC（Bidirectional Streaming RPC）**：

   - 客户端和服务端都可以发送数据流，双方互相通信。

   - 示例：

     ```
     proto
     
     
     复制代码
     rpc Chat (stream Message) returns (stream Message);
     ```

------

### gRPC 的优缺点

优点：

1. **高效**：基于 HTTP/2 和 Protobuf，具有低延迟、高吞吐量的特点。
2. **跨语言支持**：接口定义一次，可在多语言环境中使用。
3. **多样化通信模式**：支持简单 RPC、流式 RPC 和双向流式通信。
4. **可扩展性**：支持拦截器、负载均衡、服务发现等功能。
5. **自动代码生成**：减少了重复性开发工作。

缺点：

1. **调试难度较高**：二进制协议比 JSON 等文本协议更难调试。
2. **生态相对复杂**：需要熟悉 HTTP/2、Protobuf 和 gRPC 本身的工具链。
3. **学习曲线**：对于初学者来说，gRPC 的使用比 REST API 更陡峭。

------

### gRPC 的典型应用场景

1. **微服务通信**：
   - 在微服务架构中，服务之间的高效通信是核心需求。gRPC 的高性能和跨语言支持使其成为优秀的选择。
2. **实时流式通信**：
   - gRPC 的流式通信特性适合实时数据传输场景，例如音视频流、实时聊天、游戏等。
3. **高性能系统**：
   - 对延迟和吞吐量有较高要求的系统（如分布式性能监控系统、日志分析系统）可以使用 gRPC 提高通信效率。
4. **移动设备和 IoT**：
   - gRPC 的高效序列化和小数据包适合资源受限的设备，如移动设备和物联网设备。

------

如果你对某些具体的 gRPC 使用场景、工具链或底层细节有兴趣，可以进一步讨论！

### **什么是 Protocol Buffers（Protobuf）？**

Protocol Buffers（简称 Protobuf）是 Google 开发的一种**语言中立**、**平台独立**的序列化数据格式，专为高效的数据存储和通信而设计。它允许开发者定义结构化数据的格式，并提供工具将这些数据序列化为字节流（用于传输或存储），或从字节流反序列化回原始数据结构。

与传统的数据格式如 XML 或 JSON 相比，Protobuf 的体积更小，序列化和反序列化速度更快，非常适用于分布式系统和高性能场景。

------

### **Protobuf 的核心组成部分**

1. **`.proto` 文件**
   - Protobuf 的核心是 `.proto` 文件，用户通过它定义数据的结构（schema）。
   - 这种 schema 包含字段的名称、类型和 ID，并支持嵌套结构。
2. **编译器 (`protoc`)**
   - `.proto` 文件通过 `protoc` 工具编译，生成目标语言的类代码（如 C++、Java、Python 等）。这些类代码负责数据的序列化和反序列化。
3. **序列化与反序列化**
   - **序列化**：将对象或数据结构转化为紧凑的字节流。
   - **反序列化**：从字节流恢复为原始的对象或数据结构。

------

### **Protobuf 的基本工作流程**

1. **定义数据格式**
   使用 `.proto` 文件定义数据结构。例如：

   ```
   proto复制代码syntax = "proto3";
   
   message Person {
       int32 id = 1;          // 唯一ID
       string name = 2;       // 名字
       string email = 3;      // 邮箱
   }
   ```

   - `syntax = "proto3";` 表示使用 Protobuf 的第 3 个版本。
   - `message` 定义了一个消息类型（类似于 C++ 的结构体或类）。
   - 每个字段都有一个类型（如 `int32`, `string`）和唯一的标识号（如 `1`, `2`）。
   - 字段 ID 决定了数据在字节流中的存储顺序。

2. **编译 `.proto` 文件**
   使用 Protobuf 的编译器生成目标语言代码：

   ```
   bash
   
   
   复制代码
   protoc --cpp_out=. person.proto
   ```

   这会生成一个 `person.pb.h` 和 `person.pb.cc` 文件，用于在 C++ 中操作 `Person` 数据。

3. **使用生成的代码**
   在程序中通过 Protobuf 提供的类操作数据，例如：

   ```
   cpp复制代码#include "person.pb.h"
   
   Person person;
   person.set_id(1);
   person.set_name("Alice");
   person.set_email("alice@example.com");
   
   // 序列化
   std::string data;
   person.SerializeToString(&data);
   
   // 反序列化
   Person new_person;
   new_person.ParseFromString(data);
   std::cout << "Name: " << new_person.name() << std::endl;
   ```

------

### **Protobuf 的优点**

1. **紧凑的格式**
   - Protobuf 使用二进制格式，序列化后的数据体积小，性能高。
   - 与 JSON 和 XML 相比，序列化的数据通常小 3 到 10 倍。
2. **跨语言、跨平台支持**
   - 支持多种主流编程语言（如 C++、Python、Java、Go 等）。
   - 序列化数据格式是平台无关的，可以在不同系统间自由传输。
3. **强类型**
   - Protobuf 定义了数据的严格类型，可以有效避免因数据格式不匹配而引发的错误。
4. **向后兼容性**
   - 添加新字段不会破坏旧代码，删除字段后旧系统也不会出错。通过字段 ID 实现向后兼容。
5. **工具链完善**
   - Protobuf 提供了丰富的工具链（如编译器 `protoc` 和解析库），可以快速上手并高效使用。

------

### **Protobuf 的局限性**

1. **可读性差**
   - Protobuf 使用二进制格式存储数据，无法像 JSON 和 XML 那样直接阅读数据。
2. **调试不方便**
   - 虽然性能高，但调试时需要额外的工具来解析二进制数据。
3. **复杂的功能实现**
   - 比如动态字段、深度嵌套数据结构、动态 schema 更新等，可能需要额外开发工作。
4. **学习成本**
   - 初次接触时需要学习 `.proto` 语法和 Protobuf 的工具链。

------

### **Protobuf 的常见应用场景**

1. **分布式系统通信**
   - 在微服务架构中，常与 gRPC 搭配使用，作为高效的通信协议。
   - 例如服务之间的远程过程调用（RPC）。
2. **数据存储**
   - 适用于高效存储结构化数据，如数据库日志、配置文件等。
3. **网络协议**
   - 在网络通信中，作为轻量级、高性能的数据交换格式。
4. **流式数据处理**
   - 在实时数据处理框架（如 Apache Kafka）中，用于高效的数据序列化与传输。
5. **嵌入式设备**
   - 在资源有限的环境中（如 IoT 设备），Protobuf 的小体积和高性能特性尤为适用。

------

### **Protobuf 与其他序列化格式的比较**

| 特性           | Protobuf | JSON | XML  |
| -------------- | -------- | ---- | ---- |
| **格式**       | 二进制   | 文本 | 文本 |
| **体积**       | 小       | 中   | 大   |
| **速度**       | 快       | 较慢 | 慢   |
| **可读性**     | 不可读   | 可读 | 可读 |
| **跨语言支持** | 强       | 强   | 强   |
| **向后兼容性** | 好       | 差   | 差   |

------

### **Protobuf 常用高级功能**

1. **嵌套消息**

   ```
   proto复制代码message Address {
       string street = 1;
       string city = 2;
   }
   
   message Person {
       string name = 1;
       Address address = 2;
   }
   ```

2. **枚举类型**

   ```
   proto复制代码enum Status {
       UNKNOWN = 0;
       ACTIVE = 1;
       INACTIVE = 2;
   }
   ```

3. **默认值**

   - Protobuf 默认会为字段设置默认值。例如 `int32` 默认值为 0，`string` 默认值为空字符串。

4. **可选字段与 repeated 字段**

   ```
   proto复制代码message Person {
       string name = 1;              // 必须有
       optional int32 age = 2;       // 可选字段
       repeated string tags = 3;    // 可重复字段，类似数组
   }
   ```

5. **Map 类型**

   ```
   proto复制代码message PhoneBook {
       map<string, string> contacts = 1;
   }
   ```

### **CMake 的详细讲解**

CMake 是一个跨平台的构建工具，用于管理和生成项目的构建系统文件（如 Makefile、Visual Studio 项目文件等）。它非常适合复杂的项目，尤其是在需要跨平台支持和依赖管理时，CMake 提供了强大的功能。

以下是 CMake 的详细介绍，包括基本概念、使用步骤、常用命令和高级功能。

------

### **1. 什么是 CMake？**

CMake 是一种工具，用于以平台无关的方式生成构建文件。CMake 本身并不是构建系统，而是生成特定构建系统（如 Makefile 或 Visual Studio 项目）的工具。

**核心特点**

- **跨平台支持**：支持多种操作系统（如 Windows、Linux、macOS）。
- **支持多种构建工具**：如 Make、Ninja、Visual Studio 等。
- **模块化**：可以使用各种模块来管理依赖库。
- **高灵活性**：通过脚本语言（CMakeLists.txt）实现灵活的项目配置。

------

### **2. CMake 工作流程**

CMake 的工作流程分为两个阶段：

1. 配置阶段

   ：

   - CMake 读取 `CMakeLists.txt` 文件，解析用户定义的项目配置。
   - 根据配置生成适合当前平台的构建文件（如 Makefile）。

2. 生成阶段

   ：

   - 使用生成的构建文件执行构建（如调用 `make` 或 `ninja`）。

工作流程示意图：

```
scss


复制代码
CMakeLists.txt ----(cmake)----> Makefile ----(make)----> 编译输出 (如二进制文件)
```

------

### **3. 基本使用**

#### **3.1 CMakeLists.txt 文件结构**

`CMakeLists.txt` 是 CMake 项目的核心配置文件，常用语法如下：

```
cmake复制代码# 指定 CMake 最低版本
cmake_minimum_required(VERSION 3.10)

# 定义项目名称和版本
project(MyProject VERSION 1.0)

# 设置 C++ 标准
set(CMAKE_CXX_STANDARD 17)
set(CMAKE_CXX_STANDARD_REQUIRED True)

# 指定源文件
add_executable(MyExecutable main.cpp other.cpp)

# 链接库
target_link_libraries(MyExecutable PRIVATE SomeLibrary)

# 包含头文件路径
target_include_directories(MyExecutable PRIVATE include/)
```

------

#### **3.2 基本命令行使用**

假设当前目录有一个 `CMakeLists.txt` 文件，基本的 CMake 使用流程如下：

1. 创建构建目录（建议在项目根目录外创建 `build` 目录）：

   ```
   bash复制代码mkdir build
   cd build
   ```

2. 配置项目并生成构建文件：

   ```
   bash
   
   
   复制代码
   cmake ..
   ```

   - `..` 表示 `CMakeLists.txt` 文件所在目录。
   - CMake 会自动检测系统环境并生成适合的构建文件（如 Makefile）。

3. 执行构建：

   ```
   bash
   
   
   复制代码
   make
   ```

   或使用其他生成器（如 Ninja）：

   ```
   bash
   
   
   复制代码
   ninja
   ```

4. 运行生成的程序：

   ```
   bash
   
   
   复制代码
   ./MyExecutable
   ```

------

### **4. 常用命令**

#### **4.1 配置相关**

- **指定生成器**：

  ```
  bash
  
  
  复制代码
  cmake -G "Ninja" ..
  ```

  生成 Ninja 构建文件。

- **指定编译器**：

  ```
  bash
  
  
  复制代码
  cmake -DCMAKE_C_COMPILER=gcc -DCMAKE_CXX_COMPILER=g++ ..
  ```

- **设置编译类型**：

  ```
  bash
  
  
  复制代码
  cmake -DCMAKE_BUILD_TYPE=Release ..
  ```

  支持的类型：

  - `Debug`：包含调试信息，未优化。
  - `Release`：包含优化，去掉调试信息。
  - `RelWithDebInfo`：优化且包含调试信息。
  - `MinSizeRel`：以最小尺寸优化。

------

#### **4.2 构建相关**

- 直接构建

  （避免手动运行 

  ```
  make
  ```

  ）：

  ```
  bash
  
  
  复制代码
  cmake --build .
  ```

  可以添加参数指定编译类型或并行编译：

  ```
  bash
  
  
  复制代码
  cmake --build . --config Release -- -j4
  ```

------

### **5. 常用配置功能**

#### **5.1 添加外部库**

CMake 提供了多种方式来使用外部库：

1. **使用系统库**：

   ```
   cmake
   
   
   复制代码
   target_link_libraries(MyExecutable PRIVATE pthread)
   ```

2. **使用 `find_package` 查找库**： 如果依赖库提供了 CMake 配置文件，可以用 `find_package` 查找：

   ```
   cmake复制代码find_package(SomeLibrary REQUIRED)
   target_link_libraries(MyExecutable PRIVATE SomeLibrary::SomeLibrary)
   ```

3. **使用 `FetchContent` 动态下载库**： 如果依赖库未安装，可以使用 `FetchContent` 自动下载和集成：

   ```
   cmake复制代码include(FetchContent)
   FetchContent_Declare(
       googletest
       URL https://github.com/google/googletest/archive/release-1.11.0.tar.gz
   )
   FetchContent_MakeAvailable(googletest)
   
   target_link_libraries(MyExecutable PRIVATE gtest_main)
   ```

#### **5.2 设置头文件路径**

添加头文件路径：

```
cmake


复制代码
target_include_directories(MyExecutable PRIVATE include/)
```

#### **5.3 自定义输出路径**

设置编译输出路径：

```
cmake


复制代码
set(CMAKE_RUNTIME_OUTPUT_DIRECTORY ${CMAKE_BINARY_DIR}/bin)
```

------

### **6. 高级功能**

#### **6.1 构建共享库和静态库**

CMake 支持构建库文件：

1. **构建共享库**：

   ```
   cmake
   
   
   复制代码
   add_library(MySharedLibrary SHARED library.cpp)
   ```

2. **构建静态库**：

   ```
   cmake
   
   
   复制代码
   add_library(MyStaticLibrary STATIC library.cpp)
   ```

3. **链接到目标程序**：

   ```
   cmake
   
   
   复制代码
   target_link_libraries(MyExecutable PRIVATE MySharedLibrary)
   ```

#### **6.2 支持多平台和跨编译**

通过设置工具链文件支持交叉编译：

```
bash


复制代码
cmake -DCMAKE_TOOLCHAIN_FILE=toolchain.cmake ..
```

工具链文件的内容示例：

```
cmake复制代码set(CMAKE_SYSTEM_NAME Linux)
set(CMAKE_C_COMPILER arm-linux-gnueabi-gcc)
set(CMAKE_CXX_COMPILER arm-linux-gnueabi-g++)
```

#### **6.3 生成安装文件**

支持通过 `make install` 安装程序和依赖：

```
cmake复制代码install(TARGETS MyExecutable DESTINATION bin)
install(FILES header.h DESTINATION include)
```

------

### **7. CMake 与项目实践**

CMake 在现代 C++ 项目中非常流行，其模块化和扩展性在大型项目中表现尤为突出。通过以下方式优化实际开发：

- **分模块管理**：将子模块拆分为多个 CMakeLists.txt 文件，通过 `add_subdirectory` 包含。
- **结合 CI/CD 流程**：CMake 支持与 Jenkins、GitHub Actions 等工具集成，自动化构建和测试。





### **什么是项目的构建系统文件？**

**构建系统文件**是用于自动化编译、链接和生成可执行文件（或库文件）的配置文件。它告诉构建工具如何从项目的源代码生成目标文件（如可执行程序或库），并管理编译过程中的各种任务（如依赖管理、路径设置、优化选项等）。

在开发一个软件项目时，源代码通常是分散的文件（如 `.cpp`、`.h` 文件），构建系统文件可以帮我们高效地组织这些文件并生成最终的目标。

------

### **构建系统的基本概念**

构建系统文件的主要目的是**让开发者不必手动逐个编译文件**。它通过**一系列规则和配置**，自动化完成以下任务：

1. **源文件的编译**：将 `.cpp` 等源代码文件编译为目标文件（如 `.o` 或 `.obj` 文件）。
2. **链接**：将编译生成的目标文件链接为最终的可执行文件或库。
3. **依赖管理**：根据文件依赖关系（如头文件），确保每次只重新编译必要的文件。
4. **支持多平台和多工具链**：为不同平台生成对应的构建文件（如 Makefile、Visual Studio 工程文件）。
5. **管理配置选项**：支持不同的编译配置（如 Debug 和 Release 模式）。
6. **整合第三方库**：自动寻找、链接项目所依赖的外部库。

------

### **常见的构建系统工具**

构建系统文件依赖于具体的构建工具。不同工具使用不同格式的构建系统文件。以下是常见的构建工具及其构建系统文件：

| **构建工具** | **构建系统文件** | **描述**                                                     |
| ------------ | ---------------- | ------------------------------------------------------------ |
| **Make**     | `Makefile`       | 最经典的构建工具，通过规则定义文件如何生成目标。             |
| **CMake**    | `CMakeLists.txt` | 生成跨平台的构建文件，如 Makefile 或 Visual Studio 工程文件。 |
| **Ninja**    | `.ninja`         | 现代化的构建工具，专注于速度，与 CMake 配合使用。            |
| **Bazel**    | `BUILD` 文件     | 谷歌的构建工具，支持多语言和大规模构建系统。                 |
| **Meson**    | `meson.build`    | 轻量级构建工具，与 Ninja 配合使用。                          |
| **Gradle**   | `build.gradle`   | 多用于 Java/Kotlin 项目，支持依赖管理和插件扩展。            |

------

### **C++ 项目的构建系统文件**

对于 C++ 项目，最常用的构建工具是 **Make** 和 **CMake**。

#### **1. Makefile 示例**

`Makefile` 是 Make 工具使用的构建系统文件，以下是一个简单示例：

```
makefile复制代码# 定义变量
CC = g++
CFLAGS = -Wall -g

# 目标文件
TARGET = main
OBJS = main.o utils.o

# 默认目标
all: $(TARGET)

# 链接目标文件
$(TARGET): $(OBJS)
	$(CC) $(CFLAGS) -o $@ $^

# 编译每个源文件
%.o: %.cpp
	$(CC) $(CFLAGS) -c $< -o $@

# 清理
clean:
	rm -f $(TARGET) $(OBJS)
```

解释：

- **`all`** 是默认目标，用于生成最终的可执行文件。
- **`$(OBJS)`** 是依赖的目标文件列表。
- **`$@` 和 `$^`** 是自动变量，分别代表目标和所有依赖。
- **`clean`** 目标用于清理生成的文件。

执行：

```
bash复制代码make       # 编译程序
make clean # 清理文件
```

#### **2. CMakeLists.txt 示例**

CMake 是更现代的工具，以下是一个简单示例：

```
cmake复制代码# 指定 CMake 最低版本
cmake_minimum_required(VERSION 3.10)

# 定义项目名称和语言
project(MyProject VERSION 1.0 LANGUAGES CXX)

# 设置编译选项
set(CMAKE_CXX_STANDARD 17)
set(CMAKE_CXX_STANDARD_REQUIRED True)

# 添加可执行文件
add_executable(MyExecutable main.cpp utils.cpp)

# 添加头文件路径
target_include_directories(MyExecutable PRIVATE include/)
```

生成构建文件并编译：

```
bash复制代码mkdir build && cd build
cmake ..
make
```

------

### **构建系统文件的功能**

1. **多文件管理**： 构建系统文件可以组织多个源代码文件，自动处理依赖关系。例如，当你更改某个 `.h` 文件时，构建系统会重新编译所有使用它的 `.cpp` 文件。
2. **多平台支持**：
   - `Makefile` 通常用于 Unix/Linux 系统。
   - `CMakeLists.txt` 可以生成适用于 Linux（Makefile）、Windows（Visual Studio 工程文件）、macOS 的构建文件。
3. **可扩展性**： 构建系统文件可以轻松添加新的文件或功能模块。例如：
   - 添加新模块时，只需更新 `CMakeLists.txt` 或 `Makefile`。
   - 新的编译器选项或第三方库可以通过几行配置添加。
4. **自动化测试**： 构建系统文件可以集成单元测试工具，自动构建和运行测试。

------

### **手动编译和使用构建系统的对比**

#### **手动编译**

对于简单项目，你可以手动编译：

```
bash


复制代码
g++ -o main main.cpp utils.cpp
```

但这种方法存在以下问题：

1. 每次手动输入命令，效率低。
2. 当文件依赖关系复杂时，不容易管理。
3. 无法自动处理不同平台或编译器的特性。

#### **构建系统**

使用构建系统可以：

1. **自动化编译流程**：只需运行 `make` 或 `cmake --build`。
2. **管理依赖**：自动检测哪些文件需要重新编译。
3. **支持跨平台**：无需关注不同平台下的细节。

### 个人收获

对C++常用特性有了更深刻的认识，了解gRPC基本用法。





## monitor模块



### CPU负载

**CPU负载（CPU Load）** 是衡量系统在一定时间内 CPU 工作量的一种指标，它反映了系统的繁忙程度。通常来说，CPU负载表示了正在等待或已经在 CPU 上运行的进程数。

在 Linux 系统中，CPU负载通常通过三个值来表示：1分钟、5分钟、和15分钟的平均负载，分别称为 **1分钟负载**、**5分钟负载** 和 **15分钟负载**。这些数据可以从 `/proc/loadavg` 文件中获取。

### 1. **负载的含义**

- **CPU负载** 并不等同于 CPU 的使用率。它指的是在一段时间内，CPU 正在处理或等待处理的进程数量。
- 高负载并不一定意味着 CPU 完全占用，可能只是很多进程在等待 CPU 执行。如果负载过高，系统可能出现响应迟缓的情况。

### 2. **负载的计算**

CPU负载是根据系统中待运行的进程来计算的，包括：

- **运行中的进程**：这些进程正在使用 CPU 执行任务。
- **等待 CPU 的进程**：这些进程由于某些原因（如 I/O 等待）正在等待 CPU。

CPU负载的数值越高，表示系统的负担越重，CPU 越忙。常见的负载指标包括：

- **负载小于 1**：系统处于空闲状态，几乎没有进程需要 CPU 处理。
- **负载大于 1**：表示系统已经有多个进程在等待 CPU，CPU 的负担开始增加。
- **负载等于 CPU 核心数**：此时 CPU 已经有适当数量的进程在同时运行，没有进程在等待，但系统处于满负荷状态。
- **负载大于 CPU 核心数**：此时系统处于过载状态，可能会出现性能下降的情况，因为有更多的进程在等待 CPU。

### 3. **负载值的解释**

在 `/proc/loadavg` 文件中，系统的负载信息以以下格式表示：

```
yaml


复制编辑
0.35 0.45 0.50 1/345 8901
```

这个数据表示：

- **0.35**：过去 1 分钟的 CPU 平均负载。
- **0.45**：过去 5 分钟的 CPU 平均负载。
- **0.50**：过去 15 分钟的 CPU 平均负载。
- **1/345**：系统中正在运行的进程数和总进程数（正在运行的进程数 / 总进程数）。
- **8901**：最后一次进程的 ID。

### 4. **负载与 CPU 核心数的关系**

- **如果负载等于 CPU 核心数**，意味着所有的 CPU 核心都在全力工作，并且没有进程在等待 CPU。比如，如果你的系统有 4 个核心，负载为 4，那么系统的资源已经被完全利用。
- **如果负载超过 CPU 核心数**，意味着有进程在等待 CPU，系统开始变得过载，可能导致系统响应变慢，甚至引发一些性能问题。

### 5. **监控 CPU负载**

在 Linux 系统中，通常通过以下文件和命令来获取负载信息：

- `/proc/loadavg`：该文件提供了系统的负载数据，包括 1 分钟、5 分钟、15 分钟的平均负载。
- `uptime` 命令：显示系统的负载，包括过去 1 分钟、5 分钟、15 分钟的平均负载。
- `top` 或 `htop` 命令：动态显示系统负载，并列出占用 CPU 资源最多的进程。

### 6. **CPU负载与系统性能**

- **高负载并不一定意味着 CPU 使用率高**。如果负载很高，但 CPU 核心使用率仍然较低，这可能是因为有许多进程在等待 I/O 操作，而不是占用 CPU 资源。
- 如果系统负载过高，并且 CPU 使用率接近 100%，可能表示系统无法及时处理所有进程的任务，导致性能瓶颈。

### 7. **如何减少 CPU负载**

- **优化进程**：减少不必要的进程，优化进程的计算效率。
- **负载均衡**：如果是多核系统，可以通过负载均衡技术将任务分配到不同的 CPU 核心上。
- **优化 I/O 操作**：如果高负载是由于 I/O 阻塞引起的，优化文件读写或网络 I/O 操作，避免进程长时间等待。

### 8. **负载与 CPU 使用率的区别**

- **CPU使用率**：是指 CPU 在某一时刻的实际工作时间占总时间的比例。它描述的是 CPU 在工作时间中的占用情况。
- **负载**：是一个综合指标，它不仅包括 CPU 在工作中的进程数，还包括等待执行的进程数。它反映的是整个系统的繁忙程度。

### 总结

CPU负载是衡量系统负担的重要指标，它反映了系统中正在运行或等待执行的进程数量。负载的数值越高，系统的负担越大，可能会出现响应变慢的情况。通过监控 CPU负载，可以帮助我们了解系统的健康状况，并在必要时进行优化。

### 压力测试

`stress` 工具可以通过启动多个负载线程来施加不同类型的压力，主要包括以下几种：

1. **CPU压力**：模拟处理器的负载，通过使用计算密集型操作来消耗CPU资源。
   - 例如，`stress --cpu 4` 会启动4个线程，分别使用CPU进行计算密集型任务，从而增加CPU负载。
2. **内存压力**：占用大量内存资源，测试内存的处理能力。
   - 例如，`stress --vm 2 --vm-bytes 512M` 会启动2个内存负载线程，每个线程分配512MB的内存。
3. **I/O压力**：模拟磁盘读写负载，测试磁盘的读写性能。
   - 例如，`stress --io 4` 会启动4个线程进行磁盘I/O操作。
4. **压力测试多种资源**：可以同时施加CPU、内存、I/O等多种负载来模拟复杂的系统压力。
   - 例如，`stress --cpu 4 --vm 2 --io 2` 会同时增加CPU、内存和磁盘的负载。



| 方面         | 硬中断 (HardIRQ)                                  | 软中断 (SoftIRQ)                                     |
| ------------ | ------------------------------------------------- | ---------------------------------------------------- |
| **触发方式** | 由硬件设备（如外部设备、中断控制器等）触发        | 由操作系统内核或驱动程序触发                         |
| **优先级**   | 优先级较高，必须立即响应                          | 优先级较低，通常在合适的时机延迟执行                 |
| **处理方式** | 在中断上下文中直接处理，时间较短                  | 在内核上下文中处理，通常由内核调度器调度执行         |
| **使用场景** | 用于需要即时响应的任务，如硬件I/O事件、用户输入等 | 用于不需要即时响应的后台任务，如网络处理、定时任务等 |
| **执行时间** | 执行时间通常较短                                  | 执行时间相对较长，但在非紧急的背景下延迟执行         |



## 工厂模式

**工厂模式**（Factory Pattern）是一种常见的设计模式，它属于创建型模式，用于将对象的创建过程封装起来，使得代码在使用对象时不必关心其具体的创建细节。这种模式的核心思想是将实例化对象的过程交给工厂类，客户端通过工厂类来获取对象，而不是直接创建对象，从而实现了解耦和更好的代码结构。

### 工厂模式的基本概念

工厂模式包含了两个主要的角色：

1. **工厂类**：负责创建对象的实例。
2. **产品类**：是由工厂类所生产的具体类。可以是某个接口或抽象类的实现类。

工厂模式有几种常见的变体，主要包括：

- **简单工厂模式（Simple Factory Pattern）**：又称静态工厂方法模式，工厂类通过一个静态方法来根据传入的参数决定创建哪个产品类的实例。
- **工厂方法模式（Factory Method Pattern）**：工厂类通过一个工厂方法来决定实例化哪个类。
- **抽象工厂模式（Abstract Factory Pattern）**：用于创建相关或依赖对象的工厂，它提供一个接口来创建一系列相关或依赖的产品，而不需要指定具体的类。

### 1. **简单工厂模式**

简单工厂模式通常由一个工厂类和多个具体的产品类组成，工厂类根据不同的输入参数来决定创建哪个类的实例。客户端只需要调用工厂类的静态方法来获取产品实例，而无需关心实例化过程。

#### 示例代码：

```
cpp复制编辑#include <iostream>
#include <string>

// 产品类接口
class Product {
public:
    virtual void show() = 0;
};

// 具体的产品类
class ProductA : public Product {
public:
    void show() override {
        std::cout << "This is Product A." << std::endl;
    }
};

class ProductB : public Product {
public:
    void show() override {
        std::cout << "This is Product B." << std::endl;
    }
};

// 简单工厂类
class Factory {
public:
    // 根据输入的类型返回相应的产品对象
    static Product* createProduct(const std::string& type) {
        if (type == "A") {
            return new ProductA();
        } else if (type == "B") {
            return new ProductB();
        } else {
            return nullptr;
        }
    }
};

int main() {
    Product* product = Factory::createProduct("A");
    if (product) {
        product->show();
        delete product;
    }
    return 0;
}
```

#### 优缺点：

- **优点**：工厂模式使得客户端代码解耦，因为客户端不需要知道具体产品类的实现，只需要通过工厂来获取产品实例。
- **缺点**：简单工厂模式的最大问题是，如果要增加新的产品类，工厂类的代码需要修改，这违反了“开闭原则”（开放扩展，闭合修改）。

### 2. **工厂方法模式**

工厂方法模式通过为每种产品提供一个工厂方法来实现对象的创建。每个具体工厂类都继承自一个抽象工厂，并实现自己的工厂方法，从而创建不同类型的产品。工厂方法模式避免了在简单工厂模式中需要修改工厂类的情况，并且符合开闭原则。

#### 示例代码：

```
cpp复制编辑#include <iostream>

// 产品类接口
class Product {
public:
    virtual void show() = 0;
};

// 具体的产品类
class ProductA : public Product {
public:
    void show() override {
        std::cout << "This is Product A." << std::endl;
    }
};

class ProductB : public Product {
public:
    void show() override {
        std::cout << "This is Product B." << std::endl;
    }
};

// 抽象工厂类
class Factory {
public:
    virtual Product* createProduct() = 0;  // 工厂方法
};

// 具体工厂类
class FactoryA : public Factory {
public:
    Product* createProduct() override {
        return new ProductA();
    }
};

class FactoryB : public Factory {
public:
    Product* createProduct() override {
        return new ProductB();
    }
};

int main() {
    Factory* factoryA = new FactoryA();
    Product* productA = factoryA->createProduct();
    productA->show();
    delete productA;
    delete factoryA;

    Factory* factoryB = new FactoryB();
    Product* productB = factoryB->createProduct();
    productB->show();
    delete productB;
    delete factoryB;

    return 0;
}
```

#### 优缺点：

- **优点**：工厂方法模式符合开闭原则，新增产品时不需要修改现有的代码，只需要增加一个新的具体工厂类即可。
- **缺点**：每增加一个新产品，就需要增加一个新的工厂类，这会增加系统的复杂度。

### 3. **抽象工厂模式**

抽象工厂模式是工厂模式的一种扩展，它允许创建一系列相关或依赖的产品。不同于工厂方法模式只创建一种产品，抽象工厂模式可以创建多个产品系列。客户端不需要了解具体产品类，而只需要依赖于抽象工厂接口来获取产品。

#### 示例代码：

```
cpp复制编辑#include <iostream>

// 产品类接口
class ProductA {
public:
    virtual void showA() = 0;
};

class ProductB {
public:
    virtual void showB() = 0;
};

// 具体产品类
class ProductA1 : public ProductA {
public:
    void showA() override {
        std::cout << "ProductA1" << std::endl;
    }
};

class ProductB1 : public ProductB {
public:
    void showB() override {
        std::cout << "ProductB1" << std::endl;
    }
};

class ProductA2 : public ProductA {
public:
    void showA() override {
        std::cout << "ProductA2" << std::endl;
    }
};

class ProductB2 : public ProductB {
public:
    void showB() override {
        std::cout << "ProductB2" << std::endl;
    }
};

// 抽象工厂类
class AbstractFactory {
public:
    virtual ProductA* createProductA() = 0;
    virtual ProductB* createProductB() = 0;
};

// 具体工厂类
class ConcreteFactory1 : public AbstractFactory {
public:
    ProductA* createProductA() override {
        return new ProductA1();
    }
    ProductB* createProductB() override {
        return new ProductB1();
    }
};

class ConcreteFactory2 : public AbstractFactory {
public:
    ProductA* createProductA() override {
        return new ProductA2();
    }
    ProductB* createProductB() override {
        return new ProductB2();
    }
};

int main() {
    AbstractFactory* factory1 = new ConcreteFactory1();
    ProductA* productA1 = factory1->createProductA();
    ProductB* productB1 = factory1->createProductB();
    productA1->showA();
    productB1->showB();
    delete productA1;
    delete productB1;
    delete factory1;

    AbstractFactory* factory2 = new ConcreteFactory2();
    ProductA* productA2 = factory2->createProductA();
    ProductB* productB2 = factory2->createProductB();
    productA2->showA();
    productB2->showB();
    delete productA2;
    delete productB2;
    delete factory2;

    return 0;
}
```

#### 优缺点：

- **优点**：可以创建一系列相关的产品对象，并确保每个系列的产品兼容。符合开闭原则和单一责任原则，易于扩展。
- **缺点**：由于引入了多个产品和工厂类，系统的复杂度较高。

### 总结：

- **简单工厂模式**：适用于创建单一类型的产品，根据输入决定创建哪种产品。
- **工厂方法模式**：每个具体工厂负责创建自己的产品，避免了在工厂类中修改代码，符合开闭原则。
- **抽象工厂模式**：用于创建一系列相关的产品，通过抽象工厂提供一个统一的接口，避免了客户端直接依赖具体的产品类。

工厂模式的核心在于将对象的创建和使用解耦，使得系统的扩展变得更加灵活和容易管理。





## 补充：linux指令

