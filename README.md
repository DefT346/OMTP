# OMTP

OMTP - C# библиотека клиент-серверного инструментария, основанная на протоколах передачи данных TCP и UDP. 

## Быстрый старт

### Запуск сервера
<pre><code class='language-cs'>var server = OMTP.ServerModule.Server.Run("127.0.0.1", 50050, 20, typeof(ServerHandler));
</code></pre>
>[ServerHandler](#класс-обработки-на-сервере)
### Создание клиента
<pre><code class='language-cs'>var client = new OMTP.ClientModule.Client(typeof(ClientHandler));
</code></pre>
>[ClientHandler](#класс-обработки-на-клиенте)

* Поступающий на клиент или сервер пакет имеет название-ссылку на соотвевующий метод в указаных классах хендлерах (ClientHandler или ServerHandler)
* В роли идентификатора пакета участвует имя метода

### Подключение клиента
<pre><code class='language-cs'>client.Connect("127.0.0.1", 50050);
</code></pre>

### Перехват вывода внутренних логов
<pre><code class='language-cs'>OMTP.Utils.SetLogReturnAction(Log);
private void Log(string message, ConsoleColor color = ConsoleColor.White) {}
</code></pre>

### Настройка логов
Для включения/отключения вывода, изменения шаблонов форматирования логов, обратитесь к классу logSettings в экземпляре сервера или клиента и выберите необходимые параметры.

### События
Сервер
<pre><code class='language-cs'>server.OnUserConnect += AnyConnect;
void AnyConnect(Server server, int id, Packet packet) { }

server.OnUserDisconnect += AnyDisconnect;
void AnyDisconnect(Server server, Node client) { }
</code></pre>
Клиент
<pre><code class='language-cs'>client.OnConnected += Connected;
void Connected() { }

client.OnDisconnect += Disconnected;
void Disconnected() { }
</code></pre>
Глобальные
<pre><code class='language-cs'>Core.ThreadUpdate += Update;
void Update() { }
</code></pre>

## Шаблоны

### Классы обработки на сервере

<pre><code class='language-cs'>public class ServerHandler
{
    public static void MessageFromClient(OMTP.ServerModule.Server server, int fromClient, Packet packet)
    {
        string msg = packet.ReadString();
        Console.WriteLine($"Сообщение от клиента {server.GetClient(fromClient).id}:\n{msg}");
        server.SendMessageToAll(msg);
        Console.WriteLine("Сообщение было отправлено всем подключенным клиентам");
    }
}</code></pre>

<pre><code class='language-cs'>public static class ServerSend
{
    public static void SendMessageToAll(this OMTP.ServerModule.Server server, string message)
    {
        using (Packet packet = new Packet("MessageFromServer"))
        {
            packet.Write(message);
            server.SendTCPDataToAll(packet);
        }
    }
}</code></pre>

### Классы обработки на клиенте
<pre><code class='language-cs'>public static class ClientHandler
{
    public static void MessageFromServer(Packet packet)
    {
        string msg = packet.ReadString();
        Debug.Log($"Сообщение от сервера: {msg}");
    }
}</code></pre>

<pre><code class='language-cs'>public static class ClientSend
{
    public static void SendMessage(this OMTP.ClientModule.Client client, string message)
    {
        using (Packet packet = new Packet("MessageFromClient"))
        {
            packet.Write(message);
            client.SendTCPData(packet);
        }
    }
}</code></pre>
