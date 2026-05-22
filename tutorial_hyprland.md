Hyprland en Arch Linux
<img width="1920" height="1080" alt="desktop" src="https://github.com/user-attachments/assets/04f998be-774f-48af-a069-f1468dc9fe17" />
Tutorial completo para instalar y configurar Hyprland desde cero en Arch Linux, con Waybar, Rofi, notificaciones, pantalla de bloqueo y tema Catppuccin Mocha.

Que es esto
Hyprland es un compositor Wayland para Linux que permite tener un escritorio minimalista, con animaciones fluidas y totalmente personalizable. Este tutorial explica paso a paso como instalarlo en Arch Linux desde cero sin usar scripts automaticos, para entender que hace cada cosa.

Resultado final
La captura de arriba muestra el resultado con:

Waybar arriba con workspaces, hora, CPU, RAM, red y volumen
Tema Catppuccin Mocha en toda la interfaz
Rofi como lanzador de aplicaciones
Fondo de pantalla con swaybg


Indice

Instalacion base
Configuracion Hyprland
Barra Waybar
Lanzador Rofi
Fondo de pantalla
Notificaciones
Pantalla de bloqueo
WiFi
Atajos de teclado
Errores comunes
Creditos


1. Instalacion base
sudo pacman -S base-devel git
sudo pacman -S hyprland kitty waybar rofi-wayland swaybg sddm dolphin networkmanager network-manager-applet mako hyprlock nm-connection-editor
sudo systemctl enable sddm
sudo systemctl enable --now NetworkManager
Para instalar paquetes del AUR (nunca usar sudo con makepkg):
git clone https://aur.archlinux.org/paru.git
cd paru
makepkg -si
cd ~

2. Configuracion Hyprland
mkdir -p ~/.config/hypr
nano ~/.config/hypr/hyprland.conf
Ver archivo completo en configs/hyprland.conf de este repositorio.
AVISO: Usar siempre nano para editar archivos. Nunca usar cat con EOF desde SSH porque puede guardar el comando literalmente dentro del archivo.

3. Barra Waybar
mkdir -p ~/.config/waybar
cp /etc/xdg/waybar/config.jsonc ~/.config/waybar/config.jsonc
cp /etc/xdg/waybar/style.css ~/.config/waybar/style.css
nano ~/.config/waybar/config.jsonc
nano ~/.config/waybar/style.css
Ver archivos completos en configs/waybar/ de este repositorio.
AVISO: La config por defecto de waybar usa modulos de Sway que no funcionan en Hyprland. Hay que cambiar sway/workspaces por hyprland/workspaces en config.jsonc.
Para recargar waybar sin reiniciar Hyprland:
pkill waybar && waybar &

4. Lanzador Rofi
mkdir -p ~/.config/rofi
nano ~/.config/rofi/config.rasi
nano ~/.config/rofi/theme.rasi
sudo pacman -S papirus-icon-theme
Ver archivos completos en configs/rofi/ de este repositorio.
Para explorar temas incluidos:
rofi-theme-selector

5. Fondo de pantalla
Copia tu imagen desde Windows por SCP:
scp C:\Users\tuusuario\Pictures\wallpaper.jpg tuusuario@192.168.1.X:~/wallpaper.jpg
O descarga uno directamente en Arch:
curl -L -o ~/wallpaper.jpg "https://images.unsplash.com/photo-1618005182384-a83a8bd57fbe?w=1920&q=80"
Verifica que es una imagen real y no un HTML:
file ~/wallpaper.jpg
AVISO: No uses hyprpaper en versiones recientes de Hyprland, da error Missing protocols. Usa swaybg en su lugar.

6. Notificaciones
mkdir -p ~/.config/mako
nano ~/.config/mako/config
Ver archivo completo en configs/mako/config de este repositorio.

7. Pantalla de bloqueo
nano ~/.config/hypr/hyprlock.conf
Ver archivo completo en configs/hyprlock.conf de este repositorio.
Bloquea la pantalla con SUPER+L

8. WiFi
nmtui
Interfaz visual en terminal para conectarte a cualquier red WiFi sin necesidad de comandos adicionales.

9. Atajos de teclado
AccionAtajoAbrir terminalSUPER + EnterCerrar ventanaSUPER + CAbrir lanzadorSUPER + DGestor de archivosSUPER + EVentana flotanteSUPER + VPantalla completaSUPER + FBloquear pantallaSUPER + LSalir de HyprlandSUPER + MApagarSUPER + SHIFT + EReiniciarSUPER + SHIFT + RCambiar workspaceSUPER + 1 al 5Mover ventanaSUPER + SHIFT + 1-5Mover focoSUPER + flechas

10. Errores comunes
ERROR: no se permite instalar un paquete de AUR como root
SOLUCION: sal de root con exit antes de usar paru o makepkg
ERROR: Missing protocols en hyprpaper
SOLUCION: usa swaybg en su lugar
ERROR: Las apps graficas no abren desde SSH
SOLUCION: las apps graficas solo se abren desde la terminal dentro de Hyprland
ERROR: Permiso denegado en carpetas clonadas
SOLUCION: sudo chown -R USER:USER:
USER:USER ~/carpeta
ERROR: El wallpaper no carga y da error de formato
SOLUCION: comprueba con "file ~/wallpaper.jpg" que es imagen real, no HTML
ERROR: Waybar muestra modulos vacios
SOLUCION: cambia sway/workspaces por hyprland/workspaces en config.jsonc
ERROR: makepkg falla con fakeroot o debugedit
SOLUCION: instala base-devel primero con sudo pacman -S base-devel

Creditos
Tutorial creado probando cada paso en una instalacion limpia de Arch Linux.
Tema de colores: Catppuccin Mocha
