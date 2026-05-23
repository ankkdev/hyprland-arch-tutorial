# Hyprland en Arch Linux

<img width="1920" height="1080" alt="desktop" src="https://github.com/user-attachments/assets/04f998be-774f-48af-a069-f1468dc9fe17" />

Tutorial completo para instalar y configurar Hyprland desde cero en Arch Linux, con Waybar, Rofi, notificaciones, pantalla de bloqueo y tema Catppuccin Mocha.

---

## Que es esto

Hyprland es un compositor Wayland para Linux que permite tener un escritorio minimalista, con animaciones fluidas y totalmente personalizable. Este tutorial explica paso a paso como instalarlo en Arch Linux desde cero sin usar scripts automaticos, para entender que hace cada cosa.

---

## Resultado final

La captura de arriba muestra el resultado con:
- Waybar arriba con workspaces, hora, CPU, RAM, red y volumen
- Tema Catppuccin Mocha en toda la interfaz
- Rofi como lanzador de aplicaciones
- Fondo de pantalla con swaybg

---

## Indice

1. Instalacion base
2. Configuracion Hyprland
3. Barra Waybar
4. Lanzador Rofi
5. Fondo de pantalla
6. Notificaciones
7. Pantalla de bloqueo
8. WiFi
9. Atajos de teclado
10. Errores comunes
11. Creditos

---

## 1. Instalacion base

Instala las dependencias necesarias antes de cualquier otra cosa.
Sin base-devel no podras compilar paquetes del AUR:

    sudo pacman -S base-devel git

Instala todos los paquetes necesarios de una vez:

    sudo pacman -S hyprland kitty waybar rofi-wayland swaybg sddm dolphin networkmanager network-manager-applet mako hyprlock nm-connection-editor ttf-jetbrains-mono-nerd ttf-font-awesome papirus-icon-theme

Habilita los servicios:

    sudo systemctl enable sddm
    sudo systemctl enable --now NetworkManager

Instala paru para poder instalar paquetes del AUR.
AVISO: nunca uses sudo con makepkg, fallara con error de permisos:

    git clone https://aur.archlinux.org/paru.git
    cd paru
    makepkg -si
    cd ~

Si da error de permisos en la carpeta:

    sudo chown -R $USER:$USER ~/paru

---

## 2. Configuracion Hyprland

Crea la carpeta de configuracion:

    mkdir -p ~/.config/hypr

Abre el archivo con nano.
AVISO: usar siempre nano para editar archivos desde SSH.
Nunca usar cat con EOF porque puede guardar el comando literalmente dentro del archivo:

    nano ~/.config/hypr/hyprland.conf

Borra todo el contenido y pega exactamente lo siguiente.
Cambia 1920x1080 y 60 por tu resolucion y frecuencia.
Para ver las resoluciones disponibles ejecuta: hyprctl monitors

--- INICIO hyprland.conf ---
```css
monitor=,1920x1080@60,auto,1

$mainMod = SUPER
$terminal = kitty
$fileManager = dolphin
$menu = rofi -show drun

input {
    kb_layout = es
    kb_model = pc105
}

general {
    gaps_in = 5
    gaps_out = 10
    border_size = 2
    layout = dwindle
}

decoration {
    rounding = 10
}

animations {
    enabled = yes
}

exec-once = waybar
exec-once = swaybg -i ~/wallpaper.jpg -m fill
exec-once = mako
exec-once = nm-applet

bind = $mainMod, Return, exec, $terminal
bind = $mainMod, C, killactive,
bind = $mainMod, M, exit,
bind = $mainMod, D, exec, $menu
bind = $mainMod, E, exec, $fileManager
bind = $mainMod, V, togglefloating,
bind = $mainMod, F, fullscreen,
bind = $mainMod, L, exec, hyprlock
bind = $mainMod SHIFT, E, exec, systemctl poweroff
bind = $mainMod SHIFT, R, exec, systemctl reboot
bind = $mainMod, 1, workspace, 1
bind = $mainMod, 2, workspace, 2
bind = $mainMod, 3, workspace, 3
bind = $mainMod, 4, workspace, 4
bind = $mainMod, 5, workspace, 5
bind = $mainMod SHIFT, 1, movetoworkspace, 1
bind = $mainMod SHIFT, 2, movetoworkspace, 2
bind = $mainMod SHIFT, 3, movetoworkspace, 3
bind = $mainMod SHIFT, 4, movetoworkspace, 4
bind = $mainMod SHIFT, 5, movetoworkspace, 5
bind = $mainMod, left, movefocus, l
bind = $mainMod, right, movefocus, r
bind = $mainMod, up, movefocus, u
bind = $mainMod, down, movefocus, d
bind = , XF86AudioRaiseVolume, exec, pactl set-sink-volume @DEFAULT_SINK@ +5%
bind = , XF86AudioLowerVolume, exec, pactl set-sink-volume @DEFAULT_SINK@ -5%
bind = , XF86AudioMute, exec, pactl set-sink-mute @DEFAULT_SINK@ toggle
bind = $mainMod SHIFT, S, exec, grim -g "$(slurp)" ~/screenshot.png
```

--- FIN hyprland.conf ---

Guarda con Ctrl+O y sal con Ctrl+X.
Los cambios se aplican reiniciando Hyprland con SUPER+M y volviendo a entrar desde SDDM.

---

## 3. Barra Waybar

Copia la config por defecto:

    mkdir -p ~/.config/waybar
    cp /etc/xdg/waybar/config.jsonc ~/.config/waybar/config.jsonc
    cp /etc/xdg/waybar/style.css ~/.config/waybar/style.css

Abre el config:

    nano ~/.config/waybar/config.jsonc

Borra todo y pega lo siguiente.
AVISO: la config por defecto usa modulos de Sway que no funcionan en Hyprland.
Hay que usar hyprland/workspaces en lugar de sway/workspaces:

--- INICIO config.jsonc ---
```json
{
    "layer": "top",
    "position": "top",
    "modules-left": ["hyprland/workspaces"],
    "modules-center": ["clock"],
    "modules-right": ["pulseaudio", "network", "cpu", "memory"],

    "hyprland/workspaces": {
        "format": "{id}"
    },
    "clock": {
        "format": "{:%H:%M %d/%m/%Y}"
    },
    "cpu": {
        "format": "CPU {usage}%"
    },
    "memory": {
        "format": "RAM {}%"
    },
    "network": {
        "format-wifi": "{essid} {signalStrength}%",
        "format-ethernet": "ETH",
        "format-disconnected": "Sin red"
    },
    "pulseaudio": {
        "format": "VOL {volume}%",
        "format-muted": "MUTE"
    }
}
```
--- FIN config.jsonc ---

Ahora abre el estilo:

    nano ~/.config/waybar/style.css

Borra todo y pega lo siguiente (tema Catppuccin Mocha):

--- INICIO style.css ---
```css

* {
    font-family: JetBrains Mono Nerd Font;
    font-size: 13px;
    border: none;
    border-radius: 0;
    min-height: 0;
}

window#waybar {
    background: rgba(30, 30, 46, 0.9);
    color: #cdd6f4;
    border-bottom: 2px solid #89b4fa;
}

#workspaces button {
    padding: 0 8px;
    color: #6c7086;
    border-radius: 6px;
    margin: 4px 2px;
}

#workspaces button.active {
    color: #1e1e2e;
    background: #89b4fa;
}

#clock {
    padding: 0 12px;
    color: #cdd6f4;
}

#cpu {
    padding: 0 10px;
    color: #a6e3a1;
}

#memory {
    padding: 0 10px;
    color: #f9e2af;
}

#network {
    padding: 0 10px;
    color: #89dceb;
}

#pulseaudio {
    padding: 0 10px;
    color: #cba6f7;
}
```
--- FIN style.css ---

Para aplicar cambios sin reiniciar Hyprland:

    pkill waybar && waybar &

---

## 4. Lanzador Rofi

    mkdir -p ~/.config/rofi
    nano ~/.config/rofi/config.rasi

--- INICIO config.rasi ---
```css
configuration {
    modi: "drun,run,window";
    font: "JetBrains Mono 12";
    show-icons: true;
    icon-theme: "Papirus";
    drun-display-format: "{name}";
    display-drun: "Apps";
    display-run: "Run";
    display-window: "Windows";
}

@theme "~/.config/rofi/theme.rasi"

--- FIN config.rasi ---

    nano ~/.config/rofi/theme.rasi

--- INICIO theme.rasi ---
```css
* {
    bg: #1e1e2e;
    fg: #cdd6f4;
    accent: #89b4fa;
    background-color: @bg;
    text-color: @fg;
}

window {
    width: 600px;
    border-radius: 12px;
    border: 2px solid;
    border-color: @accent;
    background-color: @bg;
}

inputbar {
    padding: 12px;
    background-color: @bg;
    border-radius: 8px;
}

listview {
    padding: 8px;
    background-color: @bg;
}

element selected {
    background-color: @accent;
    text-color: @bg;
    border-radius: 6px;
}
```
--- FIN theme.rasi ---

Para explorar temas incluidos de rofi:

    rofi-theme-selector

---

## 5. Fondo de pantalla

Copia tu imagen desde Windows por SCP (recomendado):

    scp C:\Users\tuusuario\Pictures\wallpaper.jpg tuusuario@192.168.1.X:~/wallpaper.jpg

O descarga uno directamente en Arch:

    curl -L -o ~/wallpaper.jpg "https://images.unsplash.com/photo-1618005182384-a83a8bd57fbe?w=1920&q=80"

Verifica que es una imagen real y no un HTML:

    file ~/wallpaper.jpg

Debe decir JPEG image data. Si dice HTML document la descarga fallo, prueba otra URL.

AVISO: No uses hyprpaper en versiones recientes de Hyprland, da error Missing protocols. Usa swaybg en su lugar, que ya esta configurado en el exec-once del hyprland.conf.

---

## 6. Notificaciones

    mkdir -p ~/.config/mako
    nano ~/.config/mako/config

--- INICIO mako/config ---

background-color=#1e1e2e
text-color=#cdd6f4
border-color=#89b4fa
border-radius=10
border-size=2
font=JetBrains Mono 11
width=300
height=100
margin=10
padding=10

--- FIN mako/config ---

---

## 7. Pantalla de bloqueo

    nano ~/.config/hypr/hyprlock.conf

--- INICIO hyprlock.conf ---
```css
background {
    monitor =
    path = ~/wallpaper.jpg
    blur_passes = 3
}

input-field {
    monitor =
    size = 300, 50
    outline_thickness = 3
    outer_color = rgb(151515)
    inner_color = rgb(200, 200, 200)
    font_color = rgb(10, 10, 10)
    placeholder_text = Contrasena
}
```
--- FIN hyprlock.conf ---

Bloquea la pantalla con SUPER+L

---

## 8. WiFi

Interfaz visual en terminal para conectarte a cualquier red WiFi:

    nmtui

O por comandos:

    nmcli device wifi list
    nmcli device wifi connect NombreRed password tucontrasena

---

## 9. Atajos de teclado

| Accion                  | Atajo                |
|-------------------------|----------------------|
| Abrir terminal          | SUPER + Enter        |
| Cerrar ventana          | SUPER + C            |
| Abrir lanzador          | SUPER + D            |
| Gestor de archivos      | SUPER + E            |
| Ventana flotante        | SUPER + V            |
| Pantalla completa       | SUPER + F            |
| Bloquear pantalla       | SUPER + L            |
| Salir de Hyprland       | SUPER + M            |
| Apagar                  | SUPER + SHIFT + E    |
| Reiniciar               | SUPER + SHIFT + R    |
| Captura de pantalla     | SUPER + SHIFT + S    |
| Cambiar workspace       | SUPER + 1 al 5       |
| Mover ventana           | SUPER + SHIFT + 1-5  |
| Mover foco              | SUPER + flechas      |
| Subir volumen           | Tecla subir volumen  |
| Bajar volumen           | Tecla bajar volumen  |
| Silenciar               | Tecla mute           |

---

## 10. Archivos de configuracion importantes

    ~/.config/hypr/hyprland.conf   -> Todo Hyprland, atajos, monitor
    ~/.config/waybar/config.jsonc  -> Modulos de la barra
    ~/.config/waybar/style.css     -> Estilo de la barra
    ~/.config/rofi/theme.rasi      -> Tema del lanzador
    ~/.config/mako/config          -> Estilo notificaciones
    ~/.config/hypr/hyprlock.conf   -> Pantalla de bloqueo

---

## 11. Errores comunes

ERROR: no se permite instalar un paquete de AUR como root
SOLUCION: sal de root con exit antes de usar paru o makepkg

ERROR: Missing protocols en hyprpaper
SOLUCION: usa swaybg en su lugar

ERROR: Las apps graficas no abren desde SSH
SOLUCION: las apps graficas solo se abren desde la terminal dentro de Hyprland

ERROR: Permiso denegado en carpetas clonadas
SOLUCION: sudo chown -R $USER:$USER ~/carpeta

ERROR: El wallpaper no carga y da error de formato
SOLUCION: comprueba con "file ~/wallpaper.jpg" que es imagen real no HTML

ERROR: Waybar muestra modulos vacios
SOLUCION: cambia sway/workspaces por hyprland/workspaces en config.jsonc

ERROR: makepkg falla con fakeroot o debugedit
SOLUCION: instala base-devel primero con sudo pacman -S base-devel

ERROR: hyprland.conf da error en linea 1
SOLUCION: revisar que no se haya pegado texto extra en la primera linea del archivo

---

## Creditos

Tutorial creado probando cada paso en una instalacion limpia de Arch Linux.
Tema de colores: Catppuccin Mocha
