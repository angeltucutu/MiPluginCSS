using CounterStrikeSharp.API;
using CounterStrikeSharp.API.Core;
using CounterStrikeSharp.API.Modules.Menu;
using System.IO;

namespace MapMenu
{
    public class MapMenuPlugin : BasePlugin
    {
        public override string ModuleName => "Map Menu";
        public override string ModuleVersion => "1.0";
        public override string ModuleAuthor => "ChatGPT";

        public override void Load(bool hotReload)
        {
            RegisterCommand("mapmenu", "Abrir menú de mapas", OpenMapMenu);
            RegisterCommand("mm", "Abrir menú de mapas", OpenMapMenu);
        }

        private void OpenMapMenu(CCSPlayerController player, CommandInfo info)
        {
            if (player == null || !player.IsValid) return;

            var menu = new ChatMenu("Selecciona un mapa");

            foreach (var map in GetMapList())
            {
                menu.AddMenuOption(map, (p, o) =>
                {
                    Server.PrintToChatAll($"⚠ El mapa cambiará a: {map}");
                    Server.ExecuteCommand($"changelevel {map}");
                });
            }

            MenuManager.OpenChatMenu(player, menu);
        }

        private List<string> GetMapList()
        {
            var mapFiles = Directory.GetFiles("csgo/maps", "*.bsp");
            return mapFiles.Select(f => Path.GetFileNameWithoutExtension(f)).ToList();
        }
    }
}
