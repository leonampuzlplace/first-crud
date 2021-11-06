<template>
  <div id="app">
    <v-app class="paddef">
      <v-data-table
        :headers="headers"
        :items="pessoaList"
        :search="pessoaListSearch"
        sort-by="nome"
        class="elevation-1"
      >
        <template v-slot:top>
          <v-toolbar flat>
            <v-toolbar-title>Pessoas</v-toolbar-title>
            <v-divider class="mx-4" inset vertical></v-divider>
            <v-spacer></v-spacer>

            <!-- Campo de Pesquisa -->
            <v-text-field
              v-model="pessoaListSearch"
              append-icon="mdi-magnify"
              label="Pesquisar"
              single-line
              hide-details
            ></v-text-field>

            <v-spacer></v-spacer>
            <v-dialog v-model="dialog" max-width="500px">
              <template v-slot:activator="{ on, attrs }">
                <!-- Botão de Incluir -->
                <v-btn
                  color="primary"
                  dark
                  class="mb-2"
                  v-bind="attrs"
                  v-on="on"
                >
                  Incluir
                </v-btn>
              </template>

              <v-card>
                <v-card-title>
                  <span class="text-h5">{{ formTitle }}</span>
                </v-card-title>

                <!-- DBFields do Modal -->
                <v-card-text>
                  <v-container>
                    <v-row>
                      <v-col cols="12" sm="6" md="4">
                        <v-text-field
                          v-model="pessoaEntity.nome"
                          label="Nome"
                          required
                        ></v-text-field>
                      </v-col>
                      <v-col cols="12" sm="6" md="4">
                        <v-text-field
                          v-model="pessoaEntity.idade"
                          label="Idade"
                        ></v-text-field>
                      </v-col>
                      <v-col cols="12" sm="6" md="4">
                        <v-text-field
                          v-model="pessoaEntity.cel"
                          label="Celular"
                        ></v-text-field>
                      </v-col>
                      <v-col cols="12" sm="6" md="4">
                        <v-text-field
                          v-model="pessoaEntity.doc"
                          label="Documento"
                          required
                        ></v-text-field>
                      </v-col>
                    </v-row>
                  </v-container>
                </v-card-text>

                <!-- Botão de Cancelar e Salvar do Modal -->
                <v-card-actions>
                  <v-spacer></v-spacer>
                  <v-btn color="blue darken-1" text @click="closeModal">
                    Cancelar
                  </v-btn>
                  <v-btn color="blue darken-1" text @click="save">
                    Salvar
                  </v-btn>
                </v-card-actions>
              </v-card>
            </v-dialog>

            <!-- Dialog do Delete -->
            <v-dialog v-model="dialogDelete" max-width="500px">
              <v-card>
                <v-card-title class="text-h5"
                  >Deseja apagar este item?</v-card-title
                >
                <v-card-actions>
                  <v-spacer></v-spacer>
                  <v-btn color="blue darken-1" text @click="closeDelete"
                    >Cancelar</v-btn
                  >
                  <v-btn color="blue darken-1" text @click="deleteEntityConfirm"
                    >Sim, EU QUERO!</v-btn
                  >
                  <v-spacer></v-spacer>
                </v-card-actions>
              </v-card>
            </v-dialog>
          </v-toolbar>
        </template>

        <!-- Actions - Editar e Deletar da Grade -->
        <template v-slot:item.actions="{ item }">
          <v-icon small class="mr-2" @click="editEntity(item)">
            mdi-pencil
          </v-icon>
          <v-icon small @click="deleteEntity(item)"> mdi-delete </v-icon>
        </template>
      </v-data-table>
    </v-app>
  </div>
</template>


<script>
export default {
  name: "App",

  data: () => ({
    dialog: false,
    dialogDelete: false,
    headers: [
      { text: "Nome", value: "nome" },
      { text: "Idade", value: "idade" },
      { text: "Celular", value: "cel" },
      { text: "Documento", value: "doc" },
      { text: "Ações", value: "actions" },
    ],
    pessoaList: [],
    pessoaListIndex: -1,
    pessoaListSearch: "",
    pessoaEntity: {
      id: "",
      nome: "",
      idade: "",
      cel: "",
      doc: "",
    },
    pessoaEntityDefault: {
      id: "",
      nome: "",
      idade: "",
      cel: "",
      doc: "",
    } /* Ambos funcionam, o de cima e o de baixo */,
    /* pessoaEntityDefault: {}, */
  }),

  computed: {
    formTitle() {
      return this.pessoaListIndex === -1 ? "Novo" : "Alterar";
    },
  },

  watch: {
    dialog(val) {
      if (val) () => this.closeModal();
      /* val || this.closeModal(); */
    },
    dialogDelete(val) {
      if (val) () => this.closeDelete();
      /* val || this.closeDelete(); */
    },
  },

  created() {
    this.initialize();
  },

  methods: {
    initialize() {
      this.pessoaList = [
        {
          id: "13f57218-e7db-4abe-ae31-69081e63c6aa",
          nome: "Leonam José de Lima",
          idade: "17 anos",
          cel: "(19) 9 9546-7689",
          doc: "364.456.768-22",
        },
        {
          id: "c38c2497-f2f1-4c2b-b954-807cdc02f995",
          nome: "Roberto Justos",
          idade: "15 anos",
          cel: "(33) 9 9236-7619",
          doc: "631.217.710-60",
        },
        {
          id: "fceef8c8-4edf-480e-80f6-ca374230a7f5",
          nome: "Michael",
          idade: "38 anos",
          cel: "(11) 9 2378-7639",
          doc: "754.802.790-76",
        },
        {
          id: "645a1d9e-ef46-41b5-9c5d-55f368734a10",
          nome: "Fernando Salles",
          idade: "22 anos",
          cel: "(19) 9 9546-7689",
          doc: "429.931.460-33",
        },
        {
          id: "3722bb39-39ac-43dd-b132-d6f8f5657baf",
          nome: "Cristina Amaral",
          idade: "33 anos",
          cel: "(19) 9 9546-7689",
          doc: "254.663.820-35",
        },
      ];
    },

    editEntity(item) {
      this.pessoaListIndex = this.pessoaList.indexOf(item);
      this.pessoaEntity = Object.assign({}, item);
      this.dialog = true;
    },

    deleteEntity(item) {
      this.pessoaListIndex = this.pessoaList.indexOf(item);
      this.pessoaEntity = Object.assign({}, item);
      this.dialogDelete = true;
    },

    deleteEntityConfirm() {
      this.pessoaList.splice(this.pessoaListIndex, 1);
      this.closeDelete();
    },

    closeModal() {
      this.dialog = false;
      /* this.$nextTick(() => { */
      this.pessoaEntity = Object.assign({}, this.pessoaEntityDefault);
      this.pessoaListIndex = -1;
      /* }); */
    },

    closeDelete() {
      this.dialogDelete = false;
      this.$nextTick(() => {
        this.pessoaEntity = Object.assign({}, this.pessoaEntityDefault);
        this.pessoaListIndex = -1;
      });
    },

    save() {
      if (!this.pessoaEntity.nome) {
        alert("Campo [Nome] é obrigatório. ");
        return;
      }

      if (this.pessoaListIndex > -1) {
        Object.assign(this.pessoaList[this.pessoaListIndex], this.pessoaEntity);
      } else {
        this.pessoaEntity.id = this.newGuid();
        this.pessoaList.push(this.pessoaEntity);
      }
      this.closeModal();
    },

    newGuid() {
      return ([1e7] + -1e3 + -4e3 + -8e3 + -1e11).replace(/[018]/g, (c) =>
        (
          c ^
          (crypto.getRandomValues(new Uint8Array(1))[0] & (15 >> (c / 4)))
        ).toString(16)
      );
    },
  },
};
</script>

<style scoped>
.paddef {
  padding: 20px;
}
</style>