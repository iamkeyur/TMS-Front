<template>
  <v-container id="user-profile" fluid tag="section">
    <v-row justify="center">
      <v-col cols="12" md="4">
        <v-treeview
          :active.sync="active"
          :items="items"
          :load-children="fetchUsers"
          :open.sync="open"
          activatable
          transition
        >
          <template v-slot:prepend="{ item }">
            <v-icon v-if="item.children">
              {{ "mdi-folder" }}
            </v-icon>
            <v-icon v-else>
              {{ "mdi-file-document-outline" }}
            </v-icon>
          </template>
        </v-treeview>
      </v-col>

      <v-col cols="12" md="8">
        <v-scroll-y-transition mode="out-in">
          <div
            v-if="!selected"
            class="display-3 grey--text text--lighten-1 "
            style="align-self: center; width: 50%; margin: 0 auto;"
          >
            Select a Category
          </div>
          <v-form v-else ref="form">
            <v-text-field
              v-model="selected.id"
              label="Id"
              disabled
              :placeholder="selected._id"
              required
            ></v-text-field>
            <v-text-field
              v-model="selected.name"
              label="Category name"
              required
            ></v-text-field>
            <v-text-field
              v-model="selected.parentDisplayLabel"
              label="Parent Category"
              required
            ></v-text-field>

            <v-text-field
              v-model="selected.seoUrl"
              label="SEO URL"
              required
            ></v-text-field>

            <!-- <v-btn
              :disabled="!valid"
              color="success"
              class="mr-4"
              @click="validate"
            >
              Save
            </v-btn> -->

            <v-btn color="success" class="mr-4" @click="validate">
              Save
            </v-btn>

            <v-btn color="error" class="mr-4" @click="reset">
              Delete
            </v-btn>
          </v-form>
        </v-scroll-y-transition>
      </v-col>
    </v-row>
  </v-container>
</template>

<script>
const avatars = [
  "?accessoriesType=Blank&avatarStyle=Circle&clotheColor=PastelGreen&clotheType=ShirtScoopNeck&eyeType=Wink&eyebrowType=UnibrowNatural&facialHairColor=Black&facialHairType=MoustacheMagnum&hairColor=Platinum&mouthType=Concerned&skinColor=Tanned&topType=Turban",
  "?accessoriesType=Sunglasses&avatarStyle=Circle&clotheColor=Gray02&clotheType=ShirtScoopNeck&eyeType=EyeRoll&eyebrowType=RaisedExcited&facialHairColor=Red&facialHairType=BeardMagestic&hairColor=Red&hatColor=White&mouthType=Twinkle&skinColor=DarkBrown&topType=LongHairBun",
  "?accessoriesType=Prescription02&avatarStyle=Circle&clotheColor=Black&clotheType=ShirtVNeck&eyeType=Surprised&eyebrowType=Angry&facialHairColor=Blonde&facialHairType=Blank&hairColor=Blonde&hatColor=PastelOrange&mouthType=Smile&skinColor=Black&topType=LongHairNotTooLong",
  "?accessoriesType=Round&avatarStyle=Circle&clotheColor=PastelOrange&clotheType=Overall&eyeType=Close&eyebrowType=AngryNatural&facialHairColor=Blonde&facialHairType=Blank&graphicType=Pizza&hairColor=Black&hatColor=PastelBlue&mouthType=Serious&skinColor=Light&topType=LongHairBigHair",
  "?accessoriesType=Kurt&avatarStyle=Circle&clotheColor=Gray01&clotheType=BlazerShirt&eyeType=Surprised&eyebrowType=Default&facialHairColor=Red&facialHairType=Blank&graphicType=Selena&hairColor=Red&hatColor=Blue02&mouthType=Twinkle&skinColor=Pale&topType=LongHairCurly"
];

const pause = ms => new Promise(resolve => setTimeout(resolve, ms));

export default {
  data: () => ({
    active: [],
    avatar: null,
    open: [],
    en: [],
    fr: [],
    sp: [],
    zh: [],
    it: [],
    pt: [],

    categories: []
  }),

  computed: {
    items() {
      return [
        {
          id: "1",
          _id: "root",
          name: "English Categories",
          children: this.en,
          lang: "en"
        },
        {
          id: "2",
          _id: "root",
          name: "French Categories",
          children: this.fr,
          lang: "fr"
        },
        {
          id: "3",
          _id: "root",
          name: "Spanish Categories",
          children: this.sp,
          lang: "sp"
        },
        {
          id: "4",
          _id: "root",
          name: "Chienese Categories",
          children: this.zh,
          lang: "zh"
        },
        {
          id: "5",
          _id: "root",
          name: "Italian Categories",
          children: this.it,
          lang: "it"
        },
        {
          id: "6",
          _id: "root",
          name: "Portuguese Categories",
          children: this.pt,
          lang: "pt"
        }
      ];
    },
    selected() {
      console.log(this.active);
      if (!this.active.length) return undefined;

      const id = this.active[0];

      return this.categories.find(user => user.id === id);
    }
  },

  watch: {
    selected: "randomAvatar"
  },

  methods: {
    async fetchUsers(item) {
      if (item._id == "root") {
        return fetch(
          `http://3.219.121.121:12350/templates-api/get_cat_front/${item.lang}/0`
        )
          .then(res => res.json())
          .then(json => {
            item.children.push(...json);
            this.categories.push(...json);
          })
          .catch(err => console.warn(err));
      } else {
        return fetch(
          `http://3.219.121.121:12350/templates-api/get_cat_front/${item.lang}/${item._id}`
        )
          .then(res => res.json())
          .then(json => {
            item.children.push(...json);
            this.categories.push(...json);
          })
          .catch(err => console.warn(err));
      }
    },
    randomAvatar() {
      this.avatar = avatars[Math.floor(Math.random() * avatars.length)];
    }
  }
};
</script>
