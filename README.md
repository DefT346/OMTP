# OMTP

OMTP - C# библиотека клиент-серверного инструментария, с широким спектром использования, основанная на протоколах передачи данных TCP и UDP. 

## Быстрый старт

### Запуск сервера
<pre><code class='language-cs'>var server = Server.Run("127.0.0.1", 25565, 20, ServerHandlerExtensions.clientPackets);
</code></pre>
>[ServerHandlerExtensions.clientPackets](#класс-обработки-на-сервере)
### Создание клиента
<pre><code class='language-cs'>var client = new OMTP.ClientModule.Client(ClientHandlerExtensions.serverPackets);
</code></pre>
>[ClientHandlerExtensions.serverPackets](#класс-обработки-на-клиенте)

### Подключение клиента
<pre><code class='language-cs'>client.Connect("127.0.0.1", 25565);
</code></pre>

### Выгрузка логов
<pre><code class='language-cs'>OMTP.Utils.SetLogReturnAction(Log);
private void Log(string message, ConsoleColor color = ConsoleColor.White) {}
</code></pre>

## Шаблоны

### Класс обработки на сервере
<pre><code class='language-cs'>public static class ServerHandlerExtensions
{
    static ServerPackets serverPackets = new ServerPackets
        {
            "globalMessage"
        };

    public static Handlers&lt;OMTP.ServerModule.PacketHandler&gt; clientPackets = new Handlers&lt;OMTP.ServerModule.PacketHandler&gt;
        {
            { "message", ServerHandler.MessageFromClient }
        };

    public static void SendMessageToAll(this OMTP.ServerModule.Server server, string message)
    {
        using (Packet packet = new Packet(serverPackets["globalMessage"]))
        {
            packet.Write(message);
            server.SendTCPDataToAll(packet);
        }
    }

    private static class ServerHandler
    {
        public static void MessageFromClient(OMTP.ServerModule.Server server, int fromClient, Packet packet)
        {
            string msg = packet.ReadString();
            Console.WriteLine($"Сообщение от клиента {server.GetClient(fromClient).username}:\n{msg}");
            server.SendMessageToAll(msg);
            Console.WriteLine("Сообщение было отправлено всем подключенным клиентам");
        }
    }
}</code></pre>

### Класс обработки на клиенте
<pre><code class='language-cs'>public static class ClientHandlerExtensions
{
    public static Handlers&lt;OMTP.ClientModule.PacketHandler&gt; serverPackets = new Handlers&lt;OMTP.ClientModule.PacketHandler&gt;
        {
            { "globalMessage", ClientHandler.MessageFromServer }
        };

    static ClientPackets clientPackets = new ClientPackets
        {
            "message"
        };

    public static void SendMessage(this OMTP.ClientModule.Client client, string message)
    {
        using (Packet packet = new Packet(clientPackets["message"]))
        {
            packet.Write(message);
            client.SendTCPData(packet);
        }
    }
    private static class ClientHandler
    {
        public static void MessageFromServer(Packet packet)
        {
            string msg = packet.ReadString();
            Console.WriteLine($"Сообщение от сервера: {msg}");
        }
    }
}</code></pre>
