# StreamLab

Versión ligera y funcional de una plataforma de streaming en vivo al estilo Twitch:
recibe video real desde **OBS Studio** por **RTMP**, lo empaqueta a **HLS** con **ffmpeg**
(sin recodificar), lo sirve a cualquier navegador con **hls.js**, y añade chat en tiempo
real (**Socket.io**), canales, categorías, seguidores y grabación **VOD**. Todo corre en un
único proceso de Node.js con **SQLite** y se despliega en **AWS EC2** (Ubuntu 24.04, t3.micro).

## Requisitos

- Node.js 22 LTS o superior (`node -v`)
- Git for Windows (incluye Git Bash)
- ffmpeg en el PATH (en Windows instálalo con `winget install Gyan.FFmpeg`)
- OBS Studio (para transmitir; opcional si solo pruebas con ffmpeg)
- AWS CLI v2 (para el despliegue)

## Instalación y uso local

```bash
git clone https://github.com/TU_USUARIO/streamlab.git
cd streamlab
cp .env.example .env          # ajusta FFMPEG_PATH con la ruta de tu ffmpeg.exe
npm install
npm start
```

Abre <http://localhost:3000>, regístrate y entra a **Mi canal** para obtener tu servidor RTMP
y tu clave de transmisión. En OBS: *Ajustes → Emisión → Servicio: Personalizado*.

## Estructura

```
server.js        Express + API REST + Socket.io + servidor RTMP/HLS
db.js            SQLite (node:sqlite) — users, sessions, follows
public/          Frontend (HTML/CSS/JS sin frameworks)
deploy/          setup-ec2.sh — instala y arranca todo en Ubuntu
media/           (generado) HLS, VOD .mp4 y miniaturas
data/            (generado) base de datos streamlab.db
```

## Flujo de datos

```
OBS Studio → RTMP :1935 → node-media-server → ffmpeg (copy) → HLS 2 s + VOD MP4
                                                              ↓
                              Express /media + hls.js + Socket.io (chat, espectadores)
```

## Despliegue

Ver `deploy/setup-ec2.sh` y la sección 9 del manual de la práctica.
