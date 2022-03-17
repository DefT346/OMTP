# OMTP
OMTP - C# библиотека клиент-серверного инструментария, с широким спектром использования, основанная на протоколах передачи данных TCP и UDP. 

Запуск сервера
<pre><code class='language-cs'>var server = Server.Run("127.0.0.1", 25565, 20, ServerSendExtensions.clientPackets);
</code></pre>

Создание клиента
<pre><code class='language-cs'>var client = new OMTP_C.Client(ClientSendExtensions.serverPackets);
</code></pre>

Подключение клиента
<pre><code class='language-cs'>client.Connect("Name", "127.0.0.1", 25565);
</code></pre>

Выгрузка логов
<pre><code class='language-cs'>OMTP.Utils.SetInternalLogMirrorAction(Log);
private void Log(string message, ConsoleColor color = ConsoleColor.White) {}
</code></pre>

Для удобной настройки клиента/сервера ниже предлагаются шаблоны кода

ServerSettings.cs
<pre><code class='language-cs'>public static class ServerSendExtensions
{
    static ServerPackets serverPackets = new ServerPackets
        {
            "globalMessage"
        };

    public static Handlers<OMTP_S.PacketHandler> clientPackets = new Handlers<OMTP_S.PacketHandler>
        {
            { "message", ServerHandler.MessageFromClient }
        };

    public static void SendMessageToAll(this OMTP_S.Server server, string message)
    {
        using (Packet packet = new Packet(serverPackets["globalMessage"]))
        {
            packet.Write(message);
            server.SendTCPDataToAll(packet);
        }
    }

    private static class ServerHandler
    {
        public static void MessageFromClient(OMTP_S.Server server, int fromClient, Packet packet)
        {
            string msg = packet.ReadString();
            server.SendMessageToAll($"[{server.GetClient(fromClient).username}] {msg}");
        }
    }
}
</code></pre>

ClientSettings.cs
<pre><code class='language-cs'>public static class ClientSendExtensions
{
    public static Form1 form;

    public static Handlers<OMTP_C.PacketHandler> serverPackets = new Handlers<OMTP_C.PacketHandler>
        {
            { "globalMessage", ClientHandler.MessageFromServer }
        };

    static ClientPackets clientPackets = new ClientPackets
        {
            "message"
        };

    public static void SendMessage(this OMTP_C.Client client, string message)
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
            var msg = packet.ReadString();
            Form1.chatField.Invoke(new Action(() => Form1.chatField.Text += msg + Environment.NewLine));
        }
    }
}
</code></pre>
