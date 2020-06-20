<template>
  <div>
    <v-container class="mt-10">
      <table
        border="1"
        style="border-collapse: collapse;"
        width="100%"
        class="my-2"
      >
        <tr>
          <th colspan="7">
            <h2>{{ '基本情報' }}</h2>
          </th>
        </tr>
        <tr>
          <th>{{ '経典番号' }}</th>
          <th>{{ '枝番' }}</th>
          <th>{{ '経典名' }}</th>
          <th>{{ '収録巻次' }}</th>
          <th>{{ '部門' }}</th>
          <th>{{ '配本' }}</th>
          <th>{{ '出版年月日' }}</th>
        </tr>
        <tr class="text-center">
          <td>{{ data['基本情報-経典番号'] }}</td>
          <td>{{ data['基本情報-枝番'] }}</td>
          <td>
            <a :href="satUrl" target="_blank">{{ data['基本情報-経典名'] }}</a>
          </td>
          <td>{{ data['基本情報-収録巻次'] }}</td>
          <td>{{ data['基本情報-部門'] }}</td>
          <td>{{ data['基本情報-配本'] }}</td>
          <td>{{ data['基本情報-出版年月日'] }}</td>
        </tr>
      </table>

      <v-row class="text-center">
        <v-col sm="6">
          <table
            border="1"
            style="border-collapse: collapse;"
            width="100%"
            class="my-2"
          >
            <tr>
              <th colspan="4">
                <h2>{{ '勘同目録' }}</h2>
              </th>
            </tr>
            <tr>
              <th>{{ '底本/校本' }}</th>
              <th>{{ '④' }}</th>
              <th>{{ '⑦' }}</th>
              <th>{{ '⑦備考' }}</th>
            </tr>
            <tr class="text-center">
              <td>{{ data.kt }}</td>
              <td>{{ data['勘同目録-④'] }}</td>
              <td>{{ data['勘同目録-⑦'] }}</td>
              <td>{{ data['勘同目録-⑦備考'] }}</td>
            </tr>
            <tr>
              <th colspan="4">
                <h3>{{ '勘同目録詳細' }}</h3>
              </th>
            </tr>
            <template v-for="n of 5">
              <template v-if="n == 1">
                <tr v-for="n2 of 3" :key="n + n2">
                  <td v-if="n2 == 1" :rowspan="kFields.length + 3">
                    {{ 'テキスト' + n }}<br />{{ '（勘同目録）' }}
                  </td>
                  <td>-</td>
                  <td colspan="2">-</td>
                </tr>
              </template>
              <tr
                v-for="(field, index) in kFields"
                :key="n + index"
                class="text-center"
              >
                <td v-if="index == 0 && n != 1" :rowspan="kFields.length">
                  {{ 'テキスト' + n }}<br />{{ '（勘同目録）' }}
                </td>
                <td>{{ field }}</td>
                <td colspan="2">
                  {{ data.ktexts[n - 1] ? data.ktexts[n - 1][field] : '' }}
                </td>
              </tr>
            </template>

            <template v-for="n of 2">
              <tr
                v-for="(field, index) in hFields"
                :key="n + index"
                class="text-center"
              >
                <td v-if="index == 0" :rowspan="hFields.length">
                  {{ '所蔵者' + n }}<br />{{ '（勘同目録）' }}
                </td>
                <td>{{ field }}</td>
                <td colspan="2">
                  {{ data.kholds[n - 1] ? data.kholds[n - 1][field] : '' }}
                </td>
              </tr>
            </template>
          </table>
        </v-col>
        <v-col sm="6">
          <table
            border="1"
            style="border-collapse: collapse;"
            width="100%"
            class="my-2"
          >
            <tr>
              <th colspan="4">
                <h2>{{ '脚注' }}</h2>
              </th>
            </tr>
            <tr>
              <th>{{ '底本/校本' }}</th>
              <th>{{ '新添部分' }}</th>
              <th>{{ 'テキスト' }}</th>
              <th>{{ '備考' }}</th>
            </tr>
            <tr class="text-center">
              <td>{{ data.ft }}</td>
              <td>{{ data['脚注-新添'] }}</td>
              <td>
                <a href="">{{ data['脚注-テキスト'] }}</a>
              </td>
              <td>{{ data['脚注-備考'] }}</td>
            </tr>
            <tr>
              <th colspan="4">
                <h3>{{ '脚注詳細情報' }}</h3>
              </th>
            </tr>
            <template v-for="n of 5">
              <template v-if="n == 1">
                <tr :key="n">
                  <td :rowspan="kFields.length + 3">
                    {{ 'テキスト' + n }}<br />{{ '（脚注）' }}
                  </td>
                  <td>テキスト推定</td>
                  <td colspan="2"></td>
                </tr>
                <tr :key="n">
                  <td>略号使用</td>
                  <td colspan="2"></td>
                </tr>
                <tr :key="n">
                  <td>略号解説</td>
                  <td colspan="2"></td>
                </tr>
              </template>
              <tr
                v-for="(field, index) in kFields"
                :key="n + index"
                class="text-center"
              >
                <td v-if="index == 0 && n != 1" :rowspan="kFields.length">
                  {{ 'テキスト' + n }}<br />{{ '（脚注）' }}
                </td>
                <td>{{ field }}</td>
                <td colspan="2">
                  {{ data.ftexts[n - 1] ? data.ftexts[n - 1][field] : '' }}
                </td>
              </tr>
            </template>

            <template v-for="n of 2">
              <tr
                v-for="(field, index) in hFields"
                :key="n + index"
                class="text-center"
              >
                <td v-if="index == 0" :rowspan="hFields.length">
                  {{ '所蔵者' + n }}<br />{{ '（脚注）' }}
                </td>
                <td>{{ field }}</td>
                <td colspan="2">
                  {{ data.fholds[n - 1] ? data.fholds[n - 1][field] : '' }}
                </td>
              </tr>
            </template>
          </table>
        </v-col>
      </v-row>
    </v-container>
  </div>
</template>

<script lang="ts">
import { Vue, Component } from 'nuxt-property-decorator'

@Component({
  components: {},
})
export default class Search extends Vue {
  kFields: string[] = [
    '標準名称',
    '巻',
    '国',
    '時代',
    '年',
    '刊写者',
    '刊写形態',
    '関与者',
    '関与形態',
  ]

  hFields: string[] = ['国', '所蔵者']

  data: any = {
    no: '00001',
    '基本情報-経典番号': 'T0001',
    '基本情報-枝番': 1,
    '基本情報-経典名': '長阿含經',
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

    ktexts: [
      {
        標準名称: '明本',
        巻: '',
        国: '中国',
        時代: '明',
        年: '1601',
        刊写者: '',
        刊写形態: '刊',
        関与者: '',
        関与形態: '',
      },
    ],
    kholds: [],

    ft: '底本',
    '脚注-新添': '',
    '脚注-テキスト': '〔麗本〕',
    '脚注-備考': '',

    ftexts: [
      {
        標準名称: '増上寺報恩藏藏本',
        巻: '',
        国: '中国',
        時代: '明',
        年: '1589-1644',
        刊写者: '',
        刊写形態: '刊',
        関与者: '',
        関与形態: '',
      },
    ],
    fholds: [
      {
        国: '日本',
        所蔵者: '増上寺報恩藏',
      },
    ],
  }

  get satUrl(): string {
    const data = this.data
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
