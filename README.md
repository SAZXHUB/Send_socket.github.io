<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Socket Command Sender</title>
    <style>
        body {
            font-family: Arial, sans-serif;
            padding: 20px;
            background-color: #222;
            color: #fff;
        }
        .form-group {
            margin-bottom: 15px;
        }
        label {
            display: block;
            margin-bottom: 5px;
        }
        input, button {
            padding: 10px;
            font-size: 16px;
            width: 100%;
        }
        #status {
            margin-top: 20px;
        }
    </style>
</head>
<body>
    <h1>Socket Command Sender</h1>
    <div class="form-group">
        <label for="serverIp">Server IP:</label>
        <input type="text" id="serverIp" placeholder="Enter server IP">
    </div>
    <div class="form-group">
        <label for="serverPort">Server Port:</label>
        <input type="text" id="serverPort" placeholder="Enter server port">
    </div>
    <div class="form-group">
        <label for="packetSize">Size Packet:</label>
        <input type="number" id="packetSize" placeholder="Enter packet size">
    </div>
    <div class="form-group">
        <button id="toggleButton">Start Sending</button>
    </div>
    <div id="status">Status: Stopped</div>

    <script>
        let sending = false;
        let intervalId;

        document.getElementById('toggleButton').addEventListener('click', () => {
            const serverIp = document.getElementById('serverIp').value;
            let serverPort = document.getElementById('serverPort').value;
            serverPort = serverPort || '443'; // Default port to 443 if not specified

            const packetSize = document.getElementById('packetSize').value;

            if (!serverIp || !serverPort || !packetSize) {
                alert('Please enter server IP, port, and packet size.');
                return;
            }

            sending = !sending;

            if (sending) {
                document.getElementById('toggleButton').innerText = 'Stop Sending';
                document.getElementById('status').innerText = 'Status: Sending...';
                intervalId = setInterval(() => sendCommand(serverIp, serverPort, packetSize), 1000);
            } else {
                document.getElementById('toggleButton').innerText = 'Start Sending';
                document.getElementById('status').innerText = 'Status: Stopped';
                clearInterval(intervalId);
            }
        });

        function sendCommand(serverIp, serverPort, packetSize) {
            const xhr = new XMLHttpRequest();
            const url = `https://${serverIp}:${serverPort}/send-command`;
            xhr.open('POST', url, true);
            xhr.setRequestHeader('Content-Type', 'application/json');
            xhr.onreadystatechange = function () {
                if (xhr.readyState === XMLHttpRequest.DONE) {
                    if (xhr.status === 200) {
                        const response = JSON.parse(xhr.responseText);
                        document.getElementById('status').innerText = `Status: ${response.message}`;
                    } else {
                        document.getElementById('status').innerText = `Status: Error - ${xhr.status} ${xhr.statusText}`;
                    }
                }
            };
            xhr.onerror = function () {
                document.getElementById('status').innerText = `Status: Network Error (URL: ${url})`;
            };
            xhr.send(JSON.stringify({ command: "CLOSE_WEBSITE_TEMPORARILY", size: packetSize }));
        }
    </script>
</body>
</html>
