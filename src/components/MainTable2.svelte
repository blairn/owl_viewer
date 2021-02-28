<script>
  import {post_api} from './api.mjs'
  import Filter from '$components/query/Filter.svelte'

  const cleaner = (data) => data.map(d => ({
    _id:d._id,
    winrate:+d.winrate,
    games:+d.games,
    wins:+d.wins,
    Eliminations:+d.Eliminations,
    'Players Saved':+d['Players Saved'],
    'Time Played':+d['Time Played'],
    games_total:+d.games_total
  }))

  const match1 = [
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
  ]

  const match2 = [
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
  ]
  
</script>

<style>
  :root {
    color:#eeeeee;
  }
</style>
  
  <div>
    <Filter pipeline={match1} let:data={p_results1} process={cleaner}>
    <Filter pipeline={match2} let:data={p_results2} process={cleaner}>
    {#await Promise.all([p_results1,p_results2])}
    loading
    {:then data}
      <table>
      <tr><th>name</th><th>winrate for whole match</th><th>winrate for partial match</th><th>full matches played %</th><th>% played in partual games</th></tr>
      {#each data[0] as result1 (result1._id)}
      {#each data[1] as result2 (result2._id)}
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
    {/await} -
    </Filter>
    </Filter>

  </div>