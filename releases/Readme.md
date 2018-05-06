# Several Manual Releases 

Here, in this folder, you can find some releases just to test/check on NetCore (2.0 and 2.1)

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
```

## License

[MIT](http://opensource.org/licenses/MIT)