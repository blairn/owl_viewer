<script>
import {post_api} from './api.mjs'

const format = (date) => new Date(date).toLocaleDateString('en-CA')

</script>

<style>
  :root {
    color:#eeeeee;
  }
</style>

<div>
{#await post_api("overwatch/maps",[
  {
    '$group': {
      '_id': {stage:'$stage', "year":{"$year":"$round_start_time"}}, 
      'stage': {
        '$first': '$stage'
      },
      'start': {
        '$min': '$round_start_time'
      },
      'end': {
        '$max': '$round_start_time'
      },
      'count': {
        '$sum':1
      }
    }
  }, {
    '$sort': {
      'start': 1
    }
  }
])}

{:then matches}
  <table>
  <tr><th>year</th><th>stage</th><th>start</th><th>end</th><th>count</th></tr>
  {#each matches as match (match._id)}
    <tr>
      <td>{match._id.year}</td>
      <td>{match._id.stage}</td>
      <td>{format(match.start)}</td>
      <td>{format(match.end)}</td>
      <td>{match.count}</td>
    </tr>
  {/each}
  </table>
{/await}

</div>