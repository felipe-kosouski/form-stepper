<template>
  <v-stepper v-model="e1" :appraisal="appraisal">
    <v-stepper-header>
      <template v-for="n in steps">
        <v-stepper-step :key="`${n}-step`" :step="n" :complete="e1 > n" editable></v-stepper-step>
        <v-divider v-if="n !== steps" :key="n"></v-divider>
      </template>
    </v-stepper-header>
    <v-stepper-items>
      <v-stepper-content v-for="n in steps" :key="`${n}-content`" :step="n">
        <v-row align="center" justify="center">
          <v-col cols="4">
            <v-subheader class="headline">{{appraisal.appraisalCompetences[n-1].competence.name}}</v-subheader>
          </v-col>
        </v-row>

        <v-row>
          <v-col cols="12">
            <v-row justify="center">
              <v-col cols="3">
                <v-subheader class="title">Fatores</v-subheader>
              </v-col>
              <v-col cols="2">
                <v-subheader class="title">Presença da competência</v-subheader>
              </v-col>
              <v-col cols="2">
                <v-subheader class="title">Feedback do Gestor</v-subheader>
              </v-col>
              <v-col cols="2">
                <v-subheader class="title">Auto Avaliação</v-subheader>
              </v-col>
              <v-col cols="3">
                <v-subheader class="title">Feedforward</v-subheader>
              </v-col>
            </v-row>
          </v-col>
        </v-row>
        <v-divider></v-divider>
        <template v-for="competence in appraisal.appraisalCompetences[n-1]">
          <v-row v-for="(item, index) in competence.competenceSkills" :key="index">
            <v-col cols="12">
              <v-form :key="`${n}`">
                <v-row class="mb-n10" justify="center">
                  <v-col cols="3" class="mt-4">
                    <span>{{item.skill.name}}</span>
                  </v-col>

                  <v-col cols="2">
                    <v-select
                      v-model="answers[n-1][index].skillLevel"
                      outlined
                      :items="selectLevels"
                      label="Selecione"
                      item-text="level"
                      item-value="value"
                    ></v-select>
                  </v-col>
                  <v-col cols="2">
                    <v-textarea v-model="answers[n-1][index].feedBack" outlined rows="3"></v-textarea>
                  </v-col>
                  <v-col cols="2">
                    <v-textarea v-model="answers[n-1][index].selfAppraisal" outlined rows="3"></v-textarea>
                  </v-col>
                  <v-col cols="3">
                    <v-textarea v-model="answers[n-1][index].feedForward" outlined rows="3"></v-textarea>
                  </v-col>
                </v-row>
              </v-form>
            </v-col>
          </v-row>
        </template>
        <v-row>
          <v-col cols="12">
            <v-row align="center" justify="space-between">
              <v-btn tile large color="error" @click="previous(n)">
                <v-icon dark left>mdi-arrow-left</v-icon>Voltar
              </v-btn>

              <v-btn tile large color="success" @click="next(n)">
                Continuar
                <v-icon dark right>mdi-arrow-right</v-icon>
              </v-btn>
            </v-row>
          </v-col>
        </v-row>
      </v-stepper-content>
    </v-stepper-items>
  </v-stepper>
</template>

<script>
export default {
  data: () => ({
    e1: 1,
    steps: 1,
    appraisal: {
      appraisalId: 1,
      description: null,
      year: "2020",
      startDate: "2020-01-27T00:00:00",
      endDate: "2020-02-08T00:00:00",
      type: "Mandatory",
      appraised: null,
      appraisalCompetences: [
        {
          competence: {
            competenceId: 6,
            name: "Competências de Gestão",
            competenceSkills: [
              {
                skill: {
                  skillId: 4,
                  name: "Mentoria e Desenvolvimento da Equipe"
                }
              },
              {
                skill: {
                  skillId: 2,
                  name: "Comunicação com a equipe"
                }
              },
              {
                skill: {
                  skillId: 1,
                  name: "Liderança e Gestão de Pessoas"
                }
              },
              {
                skill: {
                  skillId: 3,
                  name: "Capacidade de Feedback e Feedforward"
                }
              },
              {
                skill: {
                  skillId: 5,
                  name: "Acompanhamento de tarefas, prazos de entrega"
                }
              }
            ]
          }
        },
        {
          competence: {
            competenceId: 5,
            name: "Competências Técnicas",
            competenceSkills: [
              {
                skill: {
                  skillId: 9,
                  name: "Inteligencia emocional"
                }
              },
              {
                skill: {
                  skillId: 10,
                  name: "Trabalho em equipe e cooperação"
                }
              }
            ]
          }
        },
        {
          competence: {
            competenceId: 3,
            name: "Competências Comportamentais",
            competenceSkills: [
              {
                skill: {
                  skillId: 7,
                  name: "Entusiasmos e otimismo"
                }
              },
              {
                skill: {
                  skillId: 6,
                  name: "Relacionamento Interpessoal"
                }
              },
              {
                skill: {
                  skillId: 8,
                  name: "Assiduidade"
                }
              }
            ]
          }
        }
      ],
      apFeedback: null,
      appraisalAnswer: null,
      pdi: null
    },
    selectLevels: [
      { value: 0, level: "Ausente" },
      { value: 1, level: "A Desenvolver" },
      { value: 2, level: "Satifatória" },
      { value: 3, level: "Excelencia" },
      { value: 4, level: "Não se aplica" }
    ],
    answers: [],
    competences: []
  }),
  created() {
    this.initialize();
  },
  computed: {
    skillAnswers() {
      return this.answers.map(answer => answer);
    }
  },
  methods: {
    initialize() {
      this.steps = this.appraisal.appraisalCompetences.length;
      this.appraisal.appraisalCompetences.forEach(competence => {
        this.competences.push(competence);
      });
      for (let index = 0; index < this.competences.length; index++) {
        const item = this.competences[index];
        this.answers.push([]);
        for (
          let sk_index = 0;
          sk_index < item.competence.competenceSkills.length;
          sk_index++
        ) {
          const compSkill = item.competence.competenceSkills[sk_index];
          this.answers[index].push({
            competenceId: item.competence.competenceId,
            skillId: compSkill.skill.skillId,
            skillLevel: "",
            feedBack: "",
            selfAppraisal: "",
            feedForward: ""
          });
        }
      }
      //   this.competences.forEach(item => {
      //     this.answers.push([]);
      //     item.competence.competenceSkills.forEach(compSkill => {
      //       this.addAnswers(
      //         item.competence.competenceId,
      //         compSkill.skill.skillId
      //       );
      //     });
      //   });
    },
    previous(n) {
      if (this.e1 == 1) {
        this.$router.push("/app/pdc");
      } else {
        this.e1 = n - 1;
      }
    },
    addAnswers(competenceIndex, competenceId, skillId) {
      this.answers[competenceIndex - 1].push({
        competenceId: competenceId,
        skillId: skillId,
        skillLevel: "",
        feedBack: "",
        selfAppraisal: "",
        feedForward: ""
      });
    },
    next(n) {
      if (n === this.steps) {
        this.e1 = 1;
      } else {
        this.e1 = n + 1;
      }
    }
  }
};
</script>

<style>
</style>