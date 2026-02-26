<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Sasta iPhone Giveaway! 🎁</title>
    <style>
        body { background: white; text-align: center; font-family: sans-serif; padding-top: 50px; }
        #scare { display: none; position: fixed; top: 0; left: 0; width: 100%; height: 100%; background: black; z-index: 999; }
        img { width: 100%; height: 100%; object-fit: cover; }
    </style>
</head>
<body>

    <h1>Mubarak Ho! 🎉</h1>
    <p>Aapne jeeta hai ek naya iPhone. Neeche diye gaye button par click karke claim karein:</p>
    <button onclick="startPrank()">CLAIM NOW</button>

    <div id="scare">
        <img src="https://i.imgur.com/7vY5r1R.jpg" alt="BOO!">
        <audio id="scream" src="https://www.soundboard.com/handler/DownLoadTrack.ashx?cliptitle=Scary+Scream&filename=24/244341-3b56411a-1d98-422a-8b2b-4d4b7914e7a8.mp3"></audio>
    </div>

    <script>
        function startPrank() {
            // Screen ko full screen kar dega (kuch browsers block karte hain)
            document.documentElement.requestFullscreen();
            
            // 1 second ka delay taake thoda suspense bane
            setTimeout(function() {
                document.getElementById('scare').style.display = 'block';
                var audio = document.getElementById('scream');
                audio.play();
            }, 500);
        }
    </script>

</body>
</html>
