<script>
import {post_api} from './api.mjs'

</script>

<style>

</style>

<div>
{#await post_api("overwatch/pivoted",[
  {
    '$group': {
      '_id': '$hero_name', 
      'wins': {
        '$sum': {
          '$cond': [
            '$won_map', 1, 0
          ]
        }
      }, 
      'games': {
        '$sum': 1
      }
    }
  }, {
    '$project': {
      'winrate': {
        '$divide': [
          '$wins', '$games'
        ]
      }, 
      'games': '$games', 
      'wins': '$wins'
    }
  }, {
    '$sort': {
      'winrate': 1
    }
  }
])}
loading
{:then results}
  <table width=500px>
  <tr><th>name</th><th>winrate</th><th>map wins</th><th>maps</th></tr>
  {#each results as result (result._id)}
    <tr>
      <td>{result._id}</td>
      <td>{result.winrate.toFixed(2)}</td>
      <td>{result.wins}</td>
      <td>{result.games}</td>
    </tr>
  {/each}
  </table>
{/await}

</div>