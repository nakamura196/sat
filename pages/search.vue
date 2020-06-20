<template>
  <div>
    <v-container>
      <h2 class="my-5">
        {{ $t('search_result') }}
      </h2>

      <v-data-table
        :headers="headers"
        :items="desserts"
        :items-per-page="5"
        multi-sort
      >
        <template v-slot:item.title="{ item }">
          <a :href="satUrl(item)" target="blank">{{ item.title }}</a>
        </template>

        <template v-slot:item.kt="{ item }">
          <v-chip :color="item.kt == '底本' ? 'error' : 'primary'" dark>{{
            item.kt
          }}</v-chip>
        </template>

        <template v-slot:item.ft="{ item }">
          <v-chip :color="item.ft == '底本' ? 'error' : 'primary'" dark>{{
            item.ft
          }}</v-chip>
        </template>

        <template v-slot:item.no="{ item }">
          <nuxt-link
            :to="
              localePath({
                name: 'item-id',
                params: {
                  id: item.no,
                },
              })
            "
            >{{ 'more' }}
          </nuxt-link>
        </template>
      </v-data-table>

      <v-card v-if="false">
        <v-card-title>
          <v-spacer></v-spacer>
          <!-- 
          <v-text-field
            v-model="search"
            append-icon="mdi-magnify"
            :label="$t('search')"
            single-line
            hide-details
          ></v-text-field>
          -->
        </v-card-title>
      </v-card>
    </v-container>
  </div>
</template>

<script lang="ts">
import { Vue, Component /*, Watch */ } from 'nuxt-property-decorator'
// import axios from 'axios'

// import searchResult from '~/components/ui/searchResult.vue'
import CardItem from '~/components/display/CardItem.vue'

@Component({
  components: {
    // searchResult
    CardItem,
  },
})
export default class Volumes extends Vue {
  search: string = ''
  headers: any[] = [
    {
      text: '経典番号',
      value: '基本情報-経典番号',
      filterable: false,
    },
    { text: '枝番', value: '基本情報-枝番', filterable: false },
    { text: '経典名', value: 'title', filterable: false },
    { text: '収録巻次', value: '基本情報-収録巻次', filterable: false },
    { text: '部門', value: '基本情報-部門', filterable: false },
    { text: '配本', value: '基本情報-配本', filterable: false },
    { text: '出版年月日', value: '基本情報-出版年月日', filterable: false },
    { text: '底本/校本', value: 'kt', filterable: false },
    { text: '④', value: '勘同目録-④', filterable: false },
    { text: '⑦', value: '勘同目録-⑦', filterable: false },
    { text: '⑦備考', value: '勘同目録-⑦備考', filterable: false },
    { text: '底本/校本', value: 'ft', filterable: false },
    { text: '新添部分', value: '脚注-新添', filterable: false },
    { text: 'テキスト', value: '脚注-テキスト', filterable: false },
    { text: '備考', value: '脚注-備考', filterable: false },
    { text: '詳細情報', value: 'no' },
  ]

  desserts: any[] = [
    {
      no: '00001',
      '基本情報-経典番号': 'T0001',
      '基本情報-枝番': 1,
      title: '長阿含經',
      '基本情報-収録巻次': '1',
      '基本情報-部門': '阿含部上',
      '基本情報-配本': '1',

      '基本情報-出版年月日': '19240408',

      kt: '底本',
      '勘同目録-④': '麗本',
      '勘同目録-⑦': '',
      '勘同目録-⑦備考': '',

      SAT頭出し用: {
        '経典番号-ABC': 'T0001',
        '枝番+ABC': '',
        開始巻: '1',
        ページ: '1',
        段: 'a',
        行: 1,
      },

      ft: '底本',
      '脚注-新添': '',
      '脚注-テキスト': '〔麗本〕',
      '脚注-備考': '',
    },
    {
      no: '00002',
      '基本情報-経典番号': 'T0001',
      '基本情報-枝番': 1,
      title: '長阿含經',
      '基本情報-収録巻次': '1',
      '基本情報-部門': '阿含部上',
      '基本情報-配本': '1',

      '基本情報-出版年月日': '19240408',

      kt: '校本',
      '勘同目録-④': '宋本',
      '勘同目録-⑦': '',
      '勘同目録-⑦備考': '',

      SAT頭出し用: {
        '経典番号-ABC': 'T0001',
        '枝番+ABC': '',
        開始巻: '1',
        ページ: '1',
        段: 'a',
        行: 1,
      },

      ft: '校本',
      '脚注-新添': '',
      '脚注-テキスト': '〈宋〉',
      '脚注-備考': '',
    },
  ]

  satUrl(data: any): string {
    return (
      'https://21dzk.l.u-tokyo.ac.jp/SAT2018/' +
      data['基本情報-経典番号'] +
      '_.' +
      ('00' + data['SAT頭出し用']['開始巻']).slice(-2) +
      '.' +
      ('0000' + data['SAT頭出し用']['ページ']).slice(-4) +
      data['SAT頭出し用']['段'] +
      ('00' + data['SAT頭出し用']['行']).slice(-2) +
      '.html'
    )
  }
}
</script>
