<script>
import {post_api} from './api.mjs'

</script>

<style>
  :root {
    color:#eeeeee;
  }
</style>

<div>


{#await post_api("overwatch/processed",[
  {
    "$match": {
      hero_name:{$ne:"NULL"}
    }
  }, {
    "$match": {
      adjusted_played:1
    }
  }, {
    '$group': {
      '_id': '$hero_name', 
      'wins': {
        '$sum': "$adjusted_won"
      }, 
      'games': {
        '$sum': "$adjusted_played"
      },
      'Eliminations': { "$sum": "$Eliminations"},
      'Players Saved': {"$sum": "$Players Saved"},
      'Time Played': {"$sum": "$Time Played"}
    }
  }, {
    '$project': {
      'winrate': {
        '$divide': [
          '$wins', '$games'
        ]
      }, 
      'games': '$games', 
      'wins': '$wins',
      'Eliminations': "$Eliminations",
      'Players Saved': "$Players Saved",
      'Time Played': "$Time Played"
    }
  }, {
    '$sort': {
      'winrate': 1
    }
  }
])}
loading
{:then results1}
  {#await post_api("overwatch/processed",[
  {
    "$match": {
      hero_name:{$ne:"NULL"}
    }
  }, {
    "$match": {
      adjusted_played: {$ne:1}
    }
  }, {
    '$group': {
      '_id': '$hero_name', 
      'wins': {
        '$sum': "$adjusted_won"
      }, 
      'games': {
        '$sum': "$adjusted_played"
      },
      'games_total': {
        '$sum': 1
      },
      'Eliminations': { "$sum": "$Eliminations"},
      'Players Saved': {"$sum": "$Players Saved"},
      'Time Played': {"$sum": "$Time Played"}
    }
  }, {
    '$project': {
      'winrate': {
        '$divide': [
          '$wins', '$games'
        ]
      }, 
      'games_total': '$games_total',
      'games': '$games', 
      'wins': '$wins',
      'Eliminations': "$Eliminations",
      'Players Saved': "$Players Saved",
      'Time Played': "$Time Played"
    }
  }, {
    '$sort': {
      'winrate': 1
    }
  }
])}
loading
{:then results2}
    <table>
    <tr><th>name</th><th>winrate for whole match</th><th>winrate for partial match</th><th>full matches played %</th><th>% played in partual games</th></tr>
    {#each results1 as result1 (result1._id)}
    {#each results2 as result2 (result2._id)}
    {#if result1._id == result2._id && result1._id != "All Heroes"}
      <tr>
        <td>{result1._id}</td>
        <td>{result1.winrate.toFixed(2)}</td>
        <td>{result2.winrate.toFixed(2)}</td>
        <td>{(result1.games/ (result1.games + result2.games_total) ).toFixed(2)}</td>
        <td>{(result2.games/ result2.games_total).toFixed(2)}</td>
        <!-- <td>{(result['Eliminations'] / result['Time Played'] * 60).toFixed(2)}</td>
        <td>{(result['Players Saved'] / result['Time Played'] * 60).toFixed(2)}</td> -->
      </tr>
    {/if}
    {/each}
    {/each}
    </table>
  {/await}
{/await}

</div>