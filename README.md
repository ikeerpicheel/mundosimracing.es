<!DOCTYPE html>
<html lang="es">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Mundo SimRacing</title>
    <style>
        body {
            font-family: Arial, sans-serif;
            text-align: center;
            margin: 0;
            padding: 0;
            background-color: #f4f4f4;
            transition: background 0.5s ease-in-out;
        }
        header {
            background-color: #0044cc;
            color: white;
            padding: 20px;
            font-size: 2.5em;
            transition: transform 0.3s ease-in-out;
        }
        nav {
            background-color: #003399;
            padding: 10px;
            display: flex;
            justify-content: center;
            flex-wrap: wrap;
        }
        nav a {
            margin: 10px;
            color: white;
            text-decoration: none;
            font-weight: bold;
            font-size: 1.2em;
            transition: color 0.3s ease-in-out;
        }
        .marquee {
            background: #ffcc00;
            color: black;
            padding: 10px;
            white-space: nowrap;
            overflow: hidden;
            box-sizing: border-box;
        }
        .marquee span {
            display: inline-block;
            padding-left: 100%;
            animation: marquee 15s linear infinite;
        }
        @keyframes marquee {
            from { transform: translateX(0%); }
            to { transform: translateX(-100%); }
        }
        .calendario {
            margin: 40px auto;
            width: 90%;
            max-width: 800px;
        }
        table {
            width: 100%;
            border-collapse: collapse;
        }
        td {
            width: 14%;
            padding: 10px;
            border: 1px solid #ddd;
            text-align: center;
            background-color: white;
            transition: background 0.3s, transform 0.2s;
            position: relative;
        }
        .evento {
            background-color: #ffcc00;
            color: black;
            font-weight: bold;
            border-radius: 50%;
            cursor: pointer;
        }
        .evento:hover {
            background-color: #ff9900;
            box-shadow: 0 0 10px rgba(0, 0, 0, 0.3);
        }
        .globo-evento {
            position: absolute;
            top: 0;
            left: 50%;
            transform: translateX(-50%);
            background-color: #6a2f92; /* Morado */
            color: white;
            font-weight: bold;
            padding: 5px 10px;
            border-radius: 15px;
            font-size: 0.8em;
            cursor: pointer;
        }
        .chatbot-button {
            position: fixed;
            bottom: 20px;
            right: 20px;
            background-color: #0044cc;
            color: white;
            border: none;
            padding: 15px;
            border-radius: 50%;
            font-size: 20px;
            cursor: pointer;
        }
        .chatbot-container {
            position: fixed;
            bottom: 80px;
            right: 20px;
            width: 300px;
            background: white;
            border: 1px solid #ccc;
            padding: 10px;
            box-shadow: 0 4px 8px rgba(0, 0, 0, 0.2);
            display: none;
        }
        footer {
            background-color: #0044cc;
            color: white;
            padding: 15px;
            position: fixed;
            bottom: 0;
            left: 0;
            width: 100%;
            display: flex;
            justify-content: space-between;
            align-items: center;
            flex-wrap: wrap;
        }
    </style>
</head>
<body>
    <header>Mundo SimRacing</header>
    <nav>
        <a href="ligas.html">LIGAS</a>
        <a href="podcast.html">PODCAST</a>
        <a href="comunidad.html">COMUNIDAD</a>
        <a href="login.html">INICIAR SESIÓN</a>
    </nav>
    <div class="marquee"><span>WEB EN DESARROLLO, MÁS INFO EN @mundosimracing.es</span></div>
    
    <section class="calendario">
        <h2>Calendario del Mes</h2>
        <div id="calendario">
            <!-- El calendario se generará aquí -->
        </div>
        <button onclick="cambiarMes(-1)">Mes Anterior</button>
        <button onclick="cambiarMes(1)">Mes Siguiente</button>
    </section>
    
    <button class="chatbot-button" onclick="document.getElementById('chatbot').style.display='block'">💬</button>
    <div id="chatbot" class="chatbot-container">
        <h4>Chat Soporte</h4>
        <textarea id="chat-input" rows="4" placeholder="Escribe tu mensaje..."></textarea>
        <button onclick="enviarMensaje()">Enviar</button>
    </div>

    <script>
        let fechaActual = new Date(); // Fecha actual

        // Carreras: días con eventos (por ejemplo, las carreras ocurren los días 10, 17, 24, etc.)
        const carreras = {
            2025: {
                2: [10, 17, 24, 31]  // Las carreras de febrero 2025 son el 10, 17, 24 y 31
            }
        };

        // Función para generar el calendario
        function generarCalendario() {
            const mes = fechaActual.getMonth(); // Mes actual (0-11)
            const anio = fechaActual.getFullYear(); // Año actual
            const diasEnElMes = new Date(anio, mes + 1, 0).getDate(); // Último día del mes
            const primerDiaDelMes = new Date(anio, mes, 1).getDay(); // Día de la semana del primer día (0-6)

            let html = '<table>';
            html += '<tr>';
            html += '<th>Dom</th><th>Lun</th><th>Mar</th><th>Mié</th><th>Jue</th><th>Vie</th><th>Sáb</th>';
            html += '</tr><tr>';

            // Agregar los días previos
            for (let i = 0; i < primerDiaDelMes; i++) {
                html += '<td></td>';
            }

            // Agregar los días del mes
            for (let dia = 1; dia <= diasEnElMes; dia++) {
                // Comprobar si ese día es una carrera
                const esCarrera = carreras[anio] && carreras[anio][mes + 1] && carreras[anio][mes + 1].includes(dia);
                html += `<td onclick="verEvento(${dia})">${dia}`;
                if (esCarrera) {
                    html += `<div class="globo-evento">¡Carrera!</div>`;
                }
                html += '</td>';

                // Nueva fila al final de cada semana
                if ((dia + primerDiaDelMes) % 7 === 0) {
                    html += '</tr><tr>';
                }
            }

            // Completar la última fila si es necesario
            if ((diasEnElMes + primerDiaDelMes) % 7 !== 0) {
                for (let i = (diasEnElMes + primerDiaDelMes) % 7; i < 7; i++) {
                    html += '<td></td>';
                }
            }

            html += '</tr></table>';
            document.getElementById('calendario').innerHTML = html;
        }

        // Función para cambiar de mes
        function cambiarMes(incremento) {
            fechaActual.setMonth(fechaActual.getMonth() + incremento);
            generarCalendario();
        }

        // Función para mostrar detalles de un evento (carrera)
        function verEvento(dia) {
            const mensaje = `La carrera es a las 22:00 en el día ${dia}. \nVer en: [Twitch](https://twitch.tv/vsq_league_f1)`;
            alert(mensaje);
        }

        // Inicializar el calendario al cargar la página
        generarCalendario();

        function enviarMensaje() {
            var mensaje = document.getElementById("chat-input").value;
            window.location.href = "mailto:mundosracing@gmail.com?subject=Consulta&body=" + encodeURIComponent(mensaje);
        }
    </script>
    
    <footer>
        <div>&#169; 2025 Mundo SimRacing</div>
        <div>
            <a href="#" target="_blank" style="color: white; text-decoration: none; margin-right: 15px;">Instagram</a> 
            <a href="#" target="_blank" style="color: white; text-decoration: none; margin-right: 15px;">Twitch</a> 
            <a href="#" target="_blank" style="color: white; text-decoration: none;">X</a>
        </div>
    </footer>
</body>
</html>
<!DOCTYPE html>
<html lang="es">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Ligas - Mundo SimRacing</title>
    <style>
        body {
            font-family: Arial, sans-serif;
            text-align: center;
            background-color: #f4f4f4;
            margin: 0;
            padding: 0;
        }
        header {
            background-color: #0044cc;
            color: white;
            padding: 20px;
            font-size: 2.5em;
        }
        .content {
            margin: 40px;
        }
        .league-name {
            font-size: 2em;
            font-weight: bold;
            margin-bottom: 20px;
        }
        .logos {
            display: flex;
            justify-content: center;
            gap: 30px;
            margin-top: 30px;
        }
        .logos a img {
            width: 50px;
            height: 50px;
            transition: transform 0.3s ease-in-out;
        }
        .logos a img:hover {
            transform: scale(1.2);
        }
    </style>
</head>
<body>

<header>Ligas - Mundo SimRacing</header>

<div class="content">
    <div class="league-name">Vasquinhos League</div>
    <p>¡Bienvenidos a la Vasquinhos League! Aquí podrás encontrar los enlaces para seguirnos en nuestras redes sociales.</p>
    
    <div class="logos">
        <a href="https://instagram.com/vsqleaguef1" target="_blank">
            <img src="https://upload.wikimedia.org/wikipedia/commons/thumb/9/95/Instagram_logo_2022.svg/1024px-Instagram_logo_2022.svg.png" alt="Instagram">
        </a>
        <a href="https://twitch.tv/vsq_league_f1" target="_blank">
            <img src="https://upload.wikimedia.org/wikipedia/commons/thumb/6/6a/Twitch_logo_2019.svg/1024px-Twitch_logo_2019.svg.png" alt="Twitch">
        </a>
    </div>
</div>

</body>
</html>
``

