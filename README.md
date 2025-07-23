# ads-game
/Assets/
 └── /Scripts/
      ├── PlayerStats.cs
      ├── NPCManager.cs
      ├── CombatSystem.cs
      ├── ClubManager.cs
      ├── InventorySystem.cs
      ├── GroupManager.cs
      ├── TimeWeatherSystem.cs
      ├── AdminControl.cs
using UnityEngine;

public class PlayerStats : MonoBehaviour
{
    public int health = 100;
    public float ADS = 0;  // in-game currency
    public bool isAD = false;
    public string playerName;
    public string fightingStyle = "None";

    void Update()
    {
        // Example: automatic Sunday church visit
        if (System.DateTime.Now.DayOfWeek == System.DayOfWeek.Sunday)
        {
            Debug.Log($"{playerName} is at church.");
        }
    }

    public void ApplyDamage(int amount)
    {
        health -= amount;
        if (health <= 0) Die();
    }

    void Die()
    {
        Debug.Log($"{playerName} has died. Waiting for AD permission to respawn.");
        // Add code: wait for admin to respawn
    }
}
using UnityEngine;

public class NPCManager : MonoBehaviour
{
    public NPC[] npcs;

    void Start()
    {
        // Initialize 99 NPCs
        npcs = new NPC[99];
        for (int i = 0; i < 99; i++)
        {
            npcs[i] = new NPC($"NPC_{i}");
        }
    }
}

public class NPC
{
    public string npcName;
    public string[] dialogues = {
        "Hey, watch where you’re going!",
        "Get outta my way!",
        "Freeze! Police!",
        "Put your hands up!",
        "Welcome to Ammu-Nation!"
        // add more GTA lines...
    };

    public NPC(string name)
    {
        npcName = name;
    }

    public void Speak()
    {
        string randomLine = dialogues[Random.Range(0, dialogues.Length)];
        Debug.Log($"{npcName} says: {randomLine}");
    }
}
using UnityEngine;

public class CombatSystem : MonoBehaviour
{
    public void Attack(PlayerStats attacker, PlayerStats target, string weapon, string hitLocation)
    {
        int damage = 0;

        if (weapon == "gun")
        {
            if (hitLocation == "neck") damage = 15;
            else if (hitLocation == "torso" || hitLocation == "head") damage = 5;
        }
        else if (weapon == "knife")
        {
            if (hitLocation == "neck" || hitLocation == "head") damage = attacker.health; // kill
            else damage = 10;
        }
        else if (weapon == "fist")
        {
            damage = 1;
        }

        target.ApplyDamage(damage);
    }
}
using UnityEngine;
using System.Collections.Generic;

public class InventorySystem : MonoBehaviour
{
    public List<string> inventory = new List<string>(12);
    public List<string> carTrunk = new List<string>(10);
    public List<string> fridge = new List<string>(10);
    public List<string> backpack = new List<string>(5);
    public List<string> pocket = new List<string>(2);
}
using UnityEngine;
using System.Collections.Generic;

public class ClubManager : MonoBehaviour
{
    public Dictionary<string, List<PlayerStats>> clubs = new Dictionary<string, List<PlayerStats>>();

    void Start()
    {
        string[] sports = { "FieldHockey", "Rugby", "Chess", "Cricket", "Tennis", "Basketball", "Soccer" };
        foreach (string sport in sports)
        {
            clubs[$"{sport}_Club1"] = new List<PlayerStats>();
            clubs[$"{sport}_Club2"] = new List<PlayerStats>();
        }
    }

    public void MonthlyCompetition(string clubName)
    {
        List<PlayerStats> members = clubs[clubName];
        float prizePerPlayer = 1000000f / members.Count;
        foreach (PlayerStats p in members)
        {
            p.ADS += prizePerPlayer;
            Debug.Log($"{p.playerName} won {prizePerPlayer} ADS!");
        }
    }
}
using UnityEngine;
using System.Collections.Generic;

public class Group
{
    public string name;
    public List<PlayerStats> members = new List<PlayerStats>();
    public float sharedMoney = 0;

    public void ShareItem(string item, PlayerStats giver, PlayerStats receiver)
    {
        Debug.Log($"{giver.playerName} shared {item} with {receiver.playerName}");
    }
}
using UnityEngine;

public class TimeWeatherSystem : MonoBehaviour
{
    public float gameTime = 0;
    public string weather = "Clear";

    void Update()
    {
        gameTime += Time.deltaTime; // advance time
        // every 2h real life = 24h game
        // add rain, fog, snow events here
    }
}
using UnityEngine;
using System.Collections.Generic;

public class AdminControl : MonoBehaviour
{
    public List<PlayerStats> allPlayers;
    private string secretCode = "AD1234";

    public void GiveADPower(PlayerStats player, string code)
    {
        if (code == secretCode)
        {
            player.isAD = true;
            Debug.Log($"{player.playerName} now has admin power!");
        }
    }

    public void KickPlayer(PlayerStats player)
    {
        Debug.Log($"{player.playerName} was kicked by AD.");
        // add kick logic
    }

    public void TakeItem(PlayerStats player, string item)
    {
        Debug.Log($"AD took {item} from {player.playerName}");
        // remove item from player
    }

    public void RespawnPlayer(PlayerStats player)
    {
        player.health = 100;
        Debug.Log($"{player.playerName} respawned by AD.");
    }
}
