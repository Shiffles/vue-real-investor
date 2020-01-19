<template>
    <v-container>
      <v-row>
        <v-col cols="2">
          <v-form>
            <v-text-field
              label="House Price"
              outlined
              prefix="$"
              v-model="mortgageData.purchasePrice"
              type="number"
              v-on:keyup="paymentsPlanner"
              dense
            />
            <v-text-field
              label="Down Payment"
              outlined
              prefix="$"
              v-model="mortgageData.downPayment"
              v-on:keyup="paymentsPlanner"
              dense
            />
<!--            <v-slider-->
<!--              v-model="downpay"-->
<!--              label="Down Payment"-->
<!--              step=".1"-->
<!--              ticks-->
<!--              min="0"-->
<!--              max="100">-->
<!--              <template v-slot:append>-->
<!--                <v-text-field-->
<!--                  v-model="downpay"-->
<!--                  single-line-->
<!--                  type="number"-->
<!--                  prefix="%"-->
<!--                />-->
<!--              </template>-->
<!--            </v-slider>-->
            <v-text-field
              label="Term Length"
              outlined
              suffix="Years"
              v-model="mortgageData.termLength"
              v-on:keyup="paymentsPlanner"
              dense
            />
<!--            <v-slider-->
<!--              v-model="term"-->
<!--              label="Term Length"-->
<!--              min="5"-->
<!--              max="30">-->
<!--              <template v-slot:append>-->
<!--                <v-text-field-->
<!--                  v-model="term"-->
<!--                  suffix="Years"-->
<!--                  single-line-->
<!--                  type="number"-->
<!--                />-->
<!--              </template>-->
<!--            </v-slider>-->
            <v-text-field
              label="Interest Rate"
              outlined
              suffix="%"
              v-model="mortgageData.interestRate"
              v-on:keyup="paymentsPlanner"
              dense
            />
<!--            <v-slider-->
<!--              v-model="rate"-->
<!--              step="0.1"-->
<!--              ticks-->
<!--              max="7.5"-->
<!--              label="Interest Rate">-->
<!--              <template v-slot:append>-->
<!--                <v-text-field-->
<!--                  prefix="%"-->
<!--                  v-model="rate"-->
<!--                  single-line-->
<!--                  type="number"-->
<!--                />-->
<!--              </template>-->
<!--            </v-slider>-->
            <v-text-field
              label="Annual Property Tax"
              outlined
              prefix="$"
              v-model="mortgageData.propertyTax"
              v-on:keyup="paymentsPlanner"
              dense
            />
            <v-text-field
              label="Annual Insurance"
              outlined
              prefix="$"
              v-model="mortgageData.insurancePayment"
              v-on:keyup="paymentsPlanner"
              dense
            />
            <v-text-field
              label="Monthly PMI"
              outlined
              prefix="$"
              v-model="mortgageData.pmiMonthly"
              v-on:keyup="paymentsPlanner"
              dense
            />
            <v-text-field
              label="Monthly HOA"
              outlined
              prefix="$"
              v-model="mortgageData.hoaMonthly"
              v-on:keyup="paymentsPlanner"
              dense
            />
          </v-form></v-col>
        <v-col cols="10" class="bar-chart">
          <v-row>
            <v-col cols="3" class="text-center">
              {{this.mortgageData.purchasePrice - this.mortgageData.downPayment}}
              <hr>
              <p>Loan Amount</p>
            </v-col>
            <v-col cols="3" class="text-center">
              ${{monthlyPayment}}
              <hr>
              <p>Monthly Mortgage Payment</p>
            </v-col>
            <v-col cols="3" class="text-center">
              ${{(parseFloat(monthlyPayment) + (this.mortgageData.propertyTax/12) + (this.mortgageData.insurancePayment/12) + this.mortgageData.pmiMonthly + this.mortgageData.hoaMonthly)}}
              <hr>
              <p>Total Monthly Payment</p>
            </v-col>

          </v-row>
          <MChart
            ref="chart"
            :chart-data="barChartData"
            :options="{
              maintainAspectRatio: false,
              responsive: true,
              scales: {
                xAxes: [{
                  stacked: true
                }],
                yAxes: [{
                  stacked: true
                }]
              } }"
          />
        </v-col>
      </v-row>


    </v-container>
</template>

<script>
 import MChart from '~/components/mortgage-chart'
  export default {
    name: "mortgage",
    components: {
      MChart
    },
    data () {
      return {
        mortgageData: {
          purchasePrice: 100000,
          downPayment: 0,
          termLength: 30,
          interestRate: 6,
          interestRatePercentage: 0.005,
          propertyTax: 1200,
          insurancePayment: 900,
          pmiMonthly: 0,
          hoaMonthly: 0

        },
        barChartData: null
      }
    },
    // watch: {
    //   chartData () {
    //     this.$data._chart.update()
    //   }
    // },
    computed: {
      interestRateConvert: function () {
        this.mortgageData.interestRatePercentage = ((this.mortgageData.interestRate / 100) / 12)
      },
      monthlyPayment: function () {
        this.interestRateConvert
        const m = Math.pow((1 + this.mortgageData.interestRatePercentage), (this.mortgageData.termLength * 12));
        const n = (m - 1) / (this.mortgageData.interestRatePercentage * m)
        const e = (this.mortgageData.propertyTax / 12) + (this.mortgageData.insurancePayment / 12) + this.mortgageData.pmiMonthly + this.mortgageData.hoaMonthly
        return (((this.mortgageData.purchasePrice - this.mortgageData.downPayment) / n)).toFixed(2)
      },

    },
    mounted () {
      this.paymentsPlanner()
      // this.renderChart(this.barChartData, this.options)
    },
    methods: {

      paymentsPlanner: function() {
        let cData = []
        let cLabel = []
        let mData = []
        const m = ((this.mortgageData.propertyTax/12)+(this.mortgageData.insurancePayment/12)+this.mortgageData.pmiMonthly+this.mortgageData.hoaMonthly)
        let currentLoan = this.mortgageData.purchasePrice - this.mortgageData.downPayment
        for(var i=0;i<(this.mortgageData.termLength*12);i++) {

          cData.push(currentLoan.toFixed(2).toString())
          mData.push(m)
          cLabel.push(i.toString())
          currentLoan = currentLoan - this.monthlyPayment+(currentLoan*(this.mortgageData.interestRatePercentage))
        }
        this.barChartData = {
          labels: cLabel,
            datasets: [
            {
              label: 'Mortage breakdown',
              backgroundColor: "blue",
              data: cData
            },
              {
                label: 'Monthly Additional',
                backgroundColor: "orange",
                data: mData
              }
          ]
        }
      }
    }
  }
</script>

<style scoped>

</style>
