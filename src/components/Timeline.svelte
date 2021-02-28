<script>
import Filter from '$components/query/Filter.svelte'
import {queryRunnerFor, db_url} from './api.mjs'

let brush = {}

const format = (date) => new Date(date).toLocaleDateString('en-CA')

const queryRunner = queryRunnerFor(db_url("overwatch","maps"))

const pipeline = [
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
]

</script>

<style>
  :root {
    color:#eeeeee;
  }
</style>

<div>

<Filter {pipeline} {queryRunner} bind:brush={brush} useMatches=false let:data>
  {#await data}
    loading
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
</Filter>

<!-- {#await post_api("overwatch/maps",)}
loading
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
{/await} -->

</div>