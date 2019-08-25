<template>
  <div id="juego">
    <div class="row" v-if="jugando">
      <div class="container">
        <!-- USUARIO -->
        <div class="card col m5 brown lighten-1 quitar-padding-lateral">
          <!-- Avatar -->
          <div class="card-image">
            <img
              src="https://s3.amazonaws.com/gameartpartnersimagehost/wp-content/uploads/2016/06/Chibi-Samurai-Royalty-Free-Game-Art-Featured.png"
            />
          </div>

          <!-- Barras de estado -->
          <div class="card-content grey lighten-2">
            <!-- Salud -->
            <i class="fa fa-heartbeat"></i>
            <span class="right">{{saludUsuario}}/100</span>
            <div class="progress brown">
              <div class="determinate red" v-bind:style="{width: saludUsuario + '%'}"></div>
            </div>

            <!-- Mana -->
            <i class="fa fa-magic"></i>
            <span class="right">{{manaUsuario}}/100</span>
            <div class="progress brown">
              <div class="determinate blue" v-bind:style="{width: manaUsuario + '%'}"></div>
            </div>
          </div>

          <!-- Barra de acciones -->
          <div class="card-action barra-ataques brown darken-3">
            <a class="btn-floating red" @click="normal">
              <i class="fa fa-gavel"></i>
            </a>
            <a
              v-bind:class="[manaUsuario < '10' ? 'disabled' : 'blue', 'btn-floating']"
              @click="especial"
            >
              <i class="fa fa-magic"></i>
            </a>
            <a
              v-bind:class="[manaUsuario < '15' ? 'disabled' : 'green', 'btn-floating']"
              @click="curar"
            >
              <i class="fa fa-medkit"></i>
            </a>
            <a class="btn-floating grey" @click="rendirse">
              <i class="fa fa-flag"></i>
            </a>
          </div>
        </div>

        <!-- ENEMIGO -->
        <div class="card col offset-m2 m5 brown lighten-1 quitar-padding-lateral">
          <!-- Avatar -->
          <div class="card-image">
            <img
              src="https://gameartpartners.com/wp-content/uploads/edd/2015/03/Darkness_Knight_Featured.png"
              class="girar-imagen"
            />
          </div>

          <!-- Barras de estado -->
          <div class="card-content grey lighten-2">
            <!-- Salud -->
            <i class="fa fa-heartbeat"></i>
            <span class="right">{{saludEnemigo}}/100</span>
            <div class="progress brown">
              <div class="determinate red" v-bind:style="{width: saludEnemigo + '%'}"></div>
            </div>

            <!-- Mana -->
            <i class="fa fa-magic"></i>
            <span class="right">{{manaEnemigo}}/100</span>
            <div class="progress brown">
              <div class="determinate blue" v-bind:style="{width: manaEnemigo + '%'}"></div>
            </div>
          </div>
        </div>
        <!-- Enemigo -->
      </div>
      <!-- container  -->
    </div>
    <!-- Jugando -->

    <!-- Fin del juego -->
    <div v-if-else="jugando" class="center-align">
      <a href="#" class="yellow-text mensaje-final" @click="reinciar">
        ¡{{mensaje}}!
        <h6 class="yellow-text">Click para volver a intentar</h6>
      </a>
    </div>
  </div>
  <!-- div # juego -->
</template>

<script>
import Materialize from "vue-materialize";
export default {
  name: "app",
  components: {
    Materialize
  },
  el: "#juego",
  data() {
    return {
      saludUsuario: 100,
      manaUsuario: 100,
      saludEnemigo: 100,
      manaEnemigo: 100,
      jugando: true,
      mensaje: ""
    };
  },
  methods: {
    batalla() {
      if (this.saludUsuario <= 0) {
        this.jugando = false;
        this.mensaje = "Derrota";
      }
      if (this.saludEnemigo <= 0) {
        this.jugando = false;
        this.mensaje = "Victoria";
      }
    },
    enemigo() {
      var decision = Math.floor(Math.random() * 5 + 1);

      if (
        (this.manaEnemigo < 10 && decision == 1) ||
        (this.manaEnemigo < 15 && decision == 2) ||
        (this.saludEnemigo >= 90 && decision == 2)
      ) {
        decision = 0;
      }

      switch (decision) {
        //Ataque especial enemigo
        case 1:
          this.manaEnemigo = this.manaEnemigo - 10;
          let dañoEnemigo = Math.floor(Math.random() * 5 + 1) + 5;
          this.saludUsuario = this.saludUsuario - dañoEnemigo;

          var mensaje = $(
            '<span class="blue-text">Ataque especial del enemigo pierdes ' +
              dañoEnemigo +
              " de salud</span>"
          );
          break;

        //Curación enemigo
        case 2:
          this.manaEnemigo = this.manaEnemigo - 15;
          let curacion = Math.floor(Math.random() * 5 + 1) + 5;

          if (curacion + this.saludEnemigo > 100) {
            this.saludEnemigo = 100;
          } else {
            this.saludEnemigo = this.saludEnemigo + curacion;
          }

          var mensaje = $(
            '<span class="amber-text">El enemigo se cura ' +
              curacion +
              " </span>"
          );
          break;

        //Ataque normal enemigo
        default:
          dañoEnemigo = Math.floor(Math.random() * 5 + 1);
          this.saludUsuario = this.saludUsuario - dañoEnemigo;

          var mensaje = $(
            '<span class="red-text">Pierdes ' + dañoEnemigo + " de salud</span>"
          );
          break;
      }

      // Toast
      Materialize.toast(mensaje, 3000);

      //Analizar barras de estado
      this.batalla();
    },
    normal: function() {
      let daño = Math.floor(Math.random() * 5 + 1);
      this.saludEnemigo = this.saludEnemigo - daño;

      //Toast
      Materialize.toast(
        $('<span class="green-text">Causas ' + daño + " de daño</span>"),
        2000
      );

      //Acción del enemigo
      this.enemigo();
    },
    especial: function() {
      this.manaUsuario = this.manaUsuario - 10;
      let daño = Math.floor(Math.random() * 5 + 1) + 5;
      this.saludEnemigo = this.saludEnemigo - daño;

      // Toast
      Materialize.toast(
        $(
          '<span class="cyan-text">Ataque especial causas ' +
            daño +
            " de daño</span>"
        ),
        2000
      );

      //Acción del enemigo
      this.enemigo();
    },
    curar: function() {
      this.manaUsuario = this.manaUsuario - 15;
      let curacion = Math.floor(Math.random() * 5 + 1) + 5;

      //Toast
      Materialize.toast(
        $('<span class="lime-text">Te curas ' + curacion + "</span>"),
        2000
      );

      if (curacion + this.saludUsuario > 100) {
        this.saludUsuario = 100;
      } else {
        this.saludUsuario = this.saludUsuario + curacion;
      }

      //Acción del enemigo
      this.enemigo();
    },
    rendirse: function() {
      this.jugando = false;
      this.mensaje = "Retirada";
    },
    reinciar: function() {
      this.saludUsuario = 100;
      (this.manaUsuario = 100),
        (this.saludEnemigo = 100),
        (this.manaEnemigo = 100),
        (this.jugando = true),
        (this.mensaje = "");
    }
  }
};
</script>

<style>
#app {
  font-family: "Avenir", Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
body {
  background: url("http://orig12.deviantart.net/70ab/f/2015/030/9/e/paper_mario_battle_background_by_yoshipapaercrafter-d8g09jv.png")
    center center fixed;
  min-height: 100vh;
  min-width: 100vw;
  display: flex;
  flex-direction: row;
  justify-content: space-around;
  align-items: center;
}

.barra-ataques {
  display: flex;
  flex-direction: row;
  justify-content: space-between;
}

.quitar-padding-lateral {
  padding-left: 0 !important;
  padding-right: 0 !important;
}

.mensaje-final {
  font-size: 10em;
}

.girar-imagen {
  transform: scaleX(-1);
  filter: FlipH;
}
</style>
