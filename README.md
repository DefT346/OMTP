# OMTP
OMTP - C# библиотека клиент-серверного инструментария, с широким спектром использования, основанная на протоколах передачи данных TCP и UDP. Для удобной настройки клиента/сервера ниже предлагаются шаблоны кода

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
