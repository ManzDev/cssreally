<script>
import tippy from "tippy.js";
import "tippy.js/animations/perspective-subtle.css";
import "tippy.js/dist/tippy.css";
import { toHuman } from "../modules/toHuman.js";

tippy.setDefaultProps({
  allowHTML: true,
  animation: "perspective-subtle",
  inertia: true,
});

export default {
  name: "QuizEnding",
  data() {
    return {
      score: this.$root.score,
      maxScore: this.$root.questions.length * 20,
      times: this.$root.times,
      answers: this.$root.answers,
      questions: this.$root.questions,
      maxQuestionTime: 60,
      calificationNumber: undefined,
    };
  },
  computed: {
    totalTime() {
      return this.times.reduce((p, c) => p + c);
    },
    calificationNote() {
      if (this.calificationNumber < 5) return "Insuficiente";
      else if (this.calificationNumber < 6) return "Suficiente";
      else if (this.calificationNumber < 8) return "Bien";
      else if (this.calificationNumber < 10) return "Notable";
      else return "Sobresaliente";
    },
    customMessage() {
      if (this.calificationNumber < 1)
        return "¿En serio te dedicas a esto? ¡Si hasta por error tienes muchas probabilidades de sacar una nota más alta!";
      else if (this.calificationNumber < 2)
        return "¡Oh! ¡Vaya! Que embarazoso. ¿Por casualidad no te dedicarás a la programación backend? Parece que te cuesta especialmente el CSS...";
      else if (this.calificationNumber < 3)
        return "¡Vaya, vaya! Espero que no hayas llegado aquí con la intención de sacar una buena nota, porque has fracasado miserablemente...";
      else if (this.calificationNumber < 4)
        return "Bueno, parece que algo sabes de CSS, pero aún te quedan muchas cosas que aprender en el mundo de los estilos.";
      else if (this.calificationNumber < 5)
        return "¡Uy! Casi llegas al aprobado, pero no pudo ser. CSS no es un lenguaje de programación, pero parece que es algo más difícil para ti...";
      else if (this.calificationNumber < 6)
        return "Justito, pero aprobado. Tus conocimientos de CSS indican que algo has estudiado sobre CSS. ¡No está mal!";
      else if (this.calificationNumber < 7)
        return "Te has defendido bien, joven padawan. Sin embargo, aún te queda mucho que aprender. Quizás puedas ser un gran Jedi de CSS algún día.";
      else if (this.calificationNumber < 9)
        return "Sin duda, sabes de lo que hablas. Eres un gran conocedor de CSS y dominas la materia. Felicidades.";
      else if (this.calificationNumber < 10)
        return "¡Eh! ¿Dónde aprendiste tanto sobre CSS? Pareces estar muy bien informado. ¡Enhorabuena, maestro del CSS!";
      else
        return "¡Asombroso! ¿Seguro que no has hecho trampas? Debes estar trabajando en el CSSWG en especificaciones de CSS porque esto es increíble.";
    },
  },
  created() {
    this.calificationNumber = ~~((this.score / this.maxScore) * 10 * 10) / 10;
  },
  mounted() {
    tippy(".grid > div");
  },
  methods: {
    toHuman(arg) {
      return toHuman(arg);
    },
    isCorrect(i) {
      return this.answers[i] === this.questions[i].solution;
    },
    color(value) {
      return value > 75 ? "red" : value > 45 ? "orange" : "yellow";
    },
    safe(text, i) {
      return (
        text.replace(/</g, "&lt;").replace(/>/g, "&gt;") +
        "<hr>Respondiste: " +
        this.questions[i].options[this.answers[i]]
          .replace(/</g, "&lt;")
          .replace(/>/g, "&gt;")
      );
    },
  },
};
</script>

<template>
  <div class="ending">
    <h1 class="note">
      {{ calificationNote }}
    </h1>
    <p>{{ customMessage }}</p>
    <p>
      Has obtenido un <strong>{{ calificationNumber }}</strong> y tardado
      <time>{{ toHuman(totalTime) }}</time>
    </p>
    <div class="grid">
      <div
        v-for="(q, i) in questions"
        :key="i"
        :class="{ okay: isCorrect(i) }"
        :data-tippy-content="safe(q.question, i)"
      >
        <span>#{{ i + 1 }}</span>
        <span v-if="isCorrect(i)">✔️</span>
        <span v-else>❌</span>
        <div class="progress">
          <div
            class="fill"
            :style="{
              width: `${Math.min((times[i] * 100) / maxQuestionTime, 100)}%`,
              backgroundColor: color(times[i]),
            }"
          ></div>
        </div>
      </div>
    </div>
    <div class="author">
      <p>¡Sígueme en <a href="https://twitch.tv/ManzDev">Twitch</a>!</p>
      <p>
        Creado por 🙋‍♂️ <a href="https://manz.dev/">Manz.dev</a> · 🌍
        <a href="https://lenguajecss.com/css/">LenguajeCSS</a>
      </p>
    </div>
    <div class="points">{{ score }}/{{ maxScore }}</div>
  </div>
</template>

<style src="./QuizEnding.css"></style>
