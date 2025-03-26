<!DOCTYPE html>
<html lang="pt-BR">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Gerador de XML para Filmes</title>
    <script src="https://code.jquery.com/jquery-3.6.0.min.js"></script>
    <style>
        body {
            font-family: Arial, sans-serif;
            background-color: #1e1e1e;
            color: white;
            text-align: center;
            margin: 0;
            padding: 20px;
        }
        .container {
            max-width: 800px;
            margin: auto;
            background: #2a2a2a;
            padding: 20px;
            border-radius: 10px;
            box-shadow: 0px 0px 10px rgba(255, 255, 255, 0.1);
        }
        input, textarea {
            width: 100%;
            margin-bottom: 10px;
            padding: 10px;
            background-color: #333;
            color: white;
            border: 1px solid #444;
            border-radius: 5px;
        }
        button {
            background-color: #4CAF50;
            color: white;
            padding: 10px 15px;
            border: none;
            border-radius: 5px;
            cursor: pointer;
            width: 100%;
        }
        button:hover {
            background-color: #45a049;
        }
        .output {
            margin-top: 20px;
            background-color: #333;
            padding: 15px;
            border-radius: 5px;
            white-space: pre-wrap;
            word-wrap: break-word;
            text-align: left;
        }
    </style>
</head>
<body>
    <div class="container">
        <h1>Gerador de XML para Filmes</h1>

        <!-- Formulário de Entrada -->
        <input type="text" id="titulo" placeholder="Título do Filme">
        <input type="text" id="diretor" placeholder="Diretor do Filme">
        <input type="text" id="ano" placeholder="Ano do Filme">
        <input type="text" id="duracao" placeholder="Duração do Filme (minutos)">
        <input type="text" id="nota" placeholder="Nota do Filme (0-10)">
        <input type="text" id="genero" placeholder="Gênero do Filme">
        <input type="text" id="audio" placeholder="Áudio do Filme (Ex: 5.1, Stereo)">
        <input type="text" id="thumbnail" placeholder="Link para a Thumbnail (Capa)">
        <input type="text" id="fanart" placeholder="Link para a Fanart">
        <input type="text" id="link_filme" placeholder="Link do Filme">
        <input type="text" id="link_trailer" placeholder="Link do Trailer (Opcional)">
        
        <button id="gerar">Gerar XML</button>

        <div class="output" id="xml_output"></div>
    </div>

    <script>
        $(document).ready(function() {
            $('#gerar').click(function() {
                const titulo = $('#titulo').val();
                const diretor = $('#diretor').val();
                const ano = $('#ano').val();
                const duracao = $('#duracao').val();
                const nota = $('#nota').val();
                const genero = $('#genero').val();
                const audio = $('#audio').val();
                const thumbnail = $('#thumbnail').val();
                const fanart = $('#fanart').val();
                const linkFilme = $('#link_filme').val();
                const linkTrailer = $('#link_trailer').val();

                if (!titulo || !diretor || !ano || !duracao || !nota || !genero || !audio || !thumbnail || !fanart || !linkFilme) {
                    alert('Por favor, preencha todos os campos obrigatórios!');
                    return;
                }

                // Gerar a sinopse usando a API de filmes
                $.get(`https://api.themoviedb.org/3/search/movie?api_key=1fc5f9008cebf466afb65b5a4e0cf5fa&query=${titulo}`, function(data) {
                    const sinopse = data.results.length > 0 ? data.results[0].overview : 'Sinopse não encontrada.';
                    const originalTitle = data.results.length > 0 ? data.results[0].original_title : titulo;

                    // Gerar o XML com os dados fornecidos
                    let xml = `
<?xml version="1.0" encoding="UTF-8" standalone="yes"?>
<items>
    <item>
        <title>[B]${originalTitle}[/B]</title>
        <link>${linkFilme}$$lsname=[COLOR white]${originalTitle} - FILME[/COLOR]</link>
`;

                    // Se o link do trailer for fornecido
                    if (linkTrailer) {
                        const trailerLink = `plugin://plugin.video.resolverurl_player?url=${linkTrailer}`;
                        xml += `
        <link>${trailerLink}$$lsname=[COLOR aquamarine]TRAILER[/COLOR]</link>
`;
                    }

                    // Informações adicionais
                    xml += `
        <thumbnail>${thumbnail}</thumbnail>
        <fanart>${fanart}</fanart>
        <info>
            [COLOR yellow]Título Original:[/COLOR] ${originalTitle}
            [COLOR yellow]Dirigido por:[/COLOR] ${diretor}
            [COLOR yellow]Ano:[/COLOR] ${ano}
            [COLOR yellow]Duração:[/COLOR] ${duracao} min
            [COLOR yellow]Nota:[/COLOR] ${nota}
            [COLOR yellow]Gênero:[/COLOR] ${genero}
            [COLOR yellow]Áudio:[/COLOR] ${audio}
            [COLOR yellow]Sinopse:[/COLOR] ${sinopse}
        </info>
    </item>
</items>`;

                    // Exibir o XML gerado
                    $('#xml_output').text(xml);
                });
            });
        });
    </script>
</body>
</html>
