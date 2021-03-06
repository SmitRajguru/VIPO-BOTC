<template>
  <ul class="info">
    <li class="edition" v-bind:class="['edition-' + edition]"></li>
    <li v-if="players.length - teams.traveler < 5">
      Please add more players!
    </li>
    <li>
      {{ players.length }} <font-awesome-icon class="players" icon="users" />
      {{ teams.alive }} <font-awesome-icon class="alive" icon="heartbeat" />
      {{ teams.votes }} <font-awesome-icon class="votes" icon="vote-yea" />
    </li>
    <li v-if="players.length - teams.traveler >= 5">
      {{ teams.townsfolk }}
      <font-awesome-icon class="townsfolk" icon="user-friends" />
      {{ teams.outsider }}
      <font-awesome-icon
        class="outsider"
        v-bind:icon="teams.outsider > 1 ? 'user-friends' : 'user'"
      />
      {{ teams.minion }}
      <font-awesome-icon
        class="minion"
        v-bind:icon="teams.minion > 1 ? 'user-friends' : 'user'"
      />
      {{ teams.demon }}
      <font-awesome-icon
        class="demon"
        v-bind:icon="teams.demon > 1 ? 'user-friends' : 'user'"
      />
      <template v-if="teams.traveler">
        {{ teams.traveler }}
        <font-awesome-icon
          class="traveler"
          v-bind:icon="teams.traveler > 1 ? 'user-friends' : 'user'"
        />
      </template>
    </li>
  </ul>
</template>

<script>
import gameJSON from "./../game";
import { mapState } from "vuex";

export default {
  computed: {
    teams: function() {
      const { players } = this.$store.state.players;
      const nonTravelers = this.$store.getters["players/nonTravelers"];
      const alive = players.filter(player => player.hasDied !== true).length;
      return {
        ...gameJSON[nonTravelers - 5],
        traveler: players.length - nonTravelers,
        alive,
        votes:
          alive +
          players.filter(
            player => player.hasDied === true && player.hasVoted !== true
          ).length
      };
    },
    ...mapState({
      edition: state => state.edition,
      players: state => state.players.players
    })
  }
};
</script>

<style lang="scss">
@import "../vars.scss";

// Editions
@each $img, $skipIcons in $editions {
  .edition-#{$img} {
    background-image: url("../assets/editions/#{$img}.png");
  }
  @if $skipIcons != true {
    .edition-#{$img}.townsfolk {
      background-image: url("../assets/editions/#{$img}-townsfolk.png");
    }
    .edition-#{$img}.outsider {
      background-image: url("../assets/editions/#{$img}-outsider.png");
    }
    .edition-#{$img}.minion {
      background-image: url("../assets/editions/#{$img}-minion.png");
    }
    .edition-#{$img}.demon {
      background-image: url("../assets/editions/#{$img}-demon.png");
    }
  }
}

.info {
  position: absolute;
  display: flex;
  left: 50%;
  top: 50%;
  width: 20%;
  height: 20%;
  margin-left: -10%;
  margin-top: -5%;
  padding: 50px 0 0;
  align-items: center;
  align-content: center;
  justify-content: center;
  flex-wrap: wrap;
  background: url("../assets/demon-head.png") center center no-repeat;
  background-size: auto 100%;

  li {
    display: block;
    white-space: nowrap;
    font-weight: bold;
    text-align: center;
    padding: 0 5px;
    width: 100%;
    filter: drop-shadow(0 0 2px rgba(0, 0, 0, 0.7));

    svg {
      margin-right: 10px;
    }

    .players {
      color: #00f700;
    }
    .alive {
      color: #ff4a50;
    }
    .votes {
      color: #fff;
    }
    .townsfolk {
      color: $townsfolk;
    }
    .outsider {
      color: $outsider;
    }
    .minion {
      color: $minion;
    }
    .demon {
      color: $demon;
    }
    .traveler {
      color: $traveler;
    }
  }

  li.edition {
    width: 220px;
    height: 200px;
    background-position: 0 center;
    background-repeat: no-repeat;
    background-size: 100% auto;
    position: absolute;
    top: -50px;
  }
}
</style>
