

gRPC (Google Remote Procedure Call) — это высокопроизводительный фреймворк для вызова удаленных процедур (RPC). Он позволяет клиентам и серверам взаимодействовать друг с другом через сеть, используя протокол HTTP/2 для передачи данных.

gRPC использует Protocol Buffers (Protobuf) для сериализации данных. Protobuf — это язык описания данных, который позволяет определять структуру данных и генерировать код для сериализации и десериализации этих данных.

Пример:
```
syntax = "proto3"; 

service Greeter { 
	rpc SayHello (HelloRequest) returns (HelloReply) {} 
} 

message HelloRequest { 
	string name = 1; 
} 
message HelloReply { 
	string message = 1; 
}
```
