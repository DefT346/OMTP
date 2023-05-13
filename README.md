# OMTP

OMTP - C# библиотека клиент-серверного инструментария, с широким спектром использования, основанная на протоколах передачи данных TCP и UDP. 

## Быстрый старт

### Запуск сервера
<pre><code class='language-cs'>var server = OMTP.ServerModule.Server.Run("127.0.0.1", 25565, 20, typeof(ServerHandler));
</code></pre>
>[ServerHandler](#класс-обработки-на-сервере)
### Создание клиента
<pre><code class='language-cs'>var client = new OMTP.ClientModule.Client(typeof(ClientHandler));
</code></pre>
>[ClientHandler](#класс-обработки-на-клиенте)

### Подключение клиента
<pre><code class='language-cs'>client.Connect("127.0.0.1", 25565);
</code></pre>

### Выгрузка логов
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

### Класс обработки на сервере
<pre><code class='language-cs'>public class ServerHandler
    {
        public static void Message(OMTP.ServerModule.Server server, int fromClient, Packet packet)
        {
            string msg = packet.ReadString();
            Console.WriteLine($"Сообщение от клиента {server.GetClient(fromClient).id}:\n{msg}");
            server.SendMessageToAll(msg);
            Console.WriteLine("Сообщение было отправлено всем подключенным клиентам");
        }

        public static void TestMethod(OMTP.ServerModule.Server server, int fromClient, Packet packet)
        {
        }
}</code></pre>

### Класс обработки на клиенте
<pre><code class='language-cs'>public static class ClientHandler
{
    public static void MessageFromServer(Packet packet)
    {
        string msg = packet.ReadString();
        Debug.Log($"Сообщение от сервера: {msg}");
    }

    public static void Method(Packet packet)
    {
    }
}</code></pre>
