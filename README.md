# SocketIoClientDotNet
====================

Socket.IO Client Library for .Net

* NuGet Package: [![SocketIoClientDotNet](https://img.shields.io/nuget/v/SocketIoClientDotNet.svg?maxAge=2592000)](https://www.nuget.org/packages/SocketIoClientDotNet/)

This is the Socket.IO Client Library for C#, which is ported from the [JavaScript client](https://github.com/Automattic/socket.io-client) version [1.1.0](https://github.com/socketio/socket.io-client/releases/tag/1.1.0).

See also: [EngineIoClientDotNet](https://github.com/Quobject/EngineIoClientDotNet)

## Installation
[Nuget install](https://www.nuget.org/packages/SocketIoClientDotNet/):
```
Install-Package SocketIoClientDotNet
```

## Usage
SocketIoClientDotNet has a similar api to those of the [JavaScript client](https://github.com/Automattic/socket.io-client).

```cs
using Quobject.SocketIoClientDotNet.Client;

var socket = IO.Socket("http://localhost");
socket.On(Socket.EVENT_CONNECT, () =>
{
	socket.Emit("hi");
	
});

socket.On("hi", (data) =>
	{
		Console.WriteLine(data);
		socket.Disconnect();
	});
Console.ReadLine();
```
## Usage - Version 1.0.7? (After but #152 solved) !!!

```cs

public void Connect()
{
	_socket = IO.Socket(_url);
	_socket.Connect();
}

public void Play()
{
	_socket.On("connect", () =>
	{
		_log.Write($"Connected !");

		var login = new { name = "juanlu", password = "123456" };
		_socket.Emit("login", login);
	});

	_socket.On("server_message", (msg) =>
	{
		var data = msg as string;
		_log.Write($"server_message: {data}");

		if (data.IndexOf("Logged in as") == 0)
		{
			var join = new { name = "Test room", password = "abc" };
			_socket.Emit("join_room", join);
		}
	});

	_socket.On("match_start", (msg) =>
	{
		_log.Write($"match_start: {msg}");

		var json = JsonConvert.SerializeObject(msg);
		_matchStart = JsonConvert.DeserializeObject<MatchStart>(json);
	});

	_socket.On("server_state", (msg) =>
	{
		_log.Write($"serverState: {msg}");

		var json = JsonConvert.SerializeObject(msg);
		var serverState = JsonConvert.DeserializeObject<ServerState>(json);

		if (_matchStart?.Role == 2)
		{
			PlayAsPlayer2(serverState);
		}
		else
		{
			PlayAsPlayer1(serverState);
		}
	});

	_socket.On("connect_error", (exception) =>
	{
		var ex = exception as Exception;
		_log.Write($"Error: {ex?.InnerException?.Message}");
	});
}

public void Disconnect()
{
	_socket?.Disconnect();
}

More examples can be found in [unit tests](https://github.com/Quobject/SocketIoClientDotNet/blob/master/Src/SocketIoClientDotNet.Tests.net45/ClientTests/ServerConnectionTest.cs) acting against the [test server](https://github.com/Quobject/SocketIoClientDotNet/blob/master/TestServer/server.js).

## Features
This library supports all of the features the JS client does, including events, options and upgrading transport.

## Framework Versions
.NETFramework v3.5, .NETFramework v4.0, .NETFramework v4.5, .NET Core, 2.0, 2.1

## License

[MIT](http://opensource.org/licenses/MIT)
