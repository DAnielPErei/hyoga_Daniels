<script lang="ts">
import MInputNumber from '@/components/MInputNumber.vue';
import MainAdsenseBottom from '@/components/MainAdsenseBottom.vue';
import { formatCurrency } from '@/utils/Numbers';

export default {
    name: 'MonthlyInvestimentSimulator',
    components: { MainAdsenseBottom, MInputNumber },
    head() {
        return {
            title: 'Simulador de Aporte Mensal',
            meta: [
                {
                    name: 'description',
                    content: 'Simulador de aporte mensal para atingir um patrimônio alvo. Calcule quanto você deve investir mensalmente para atingir um objetivo financeiro.'
                }
            ]
        };
    },
    data() {
        return {
            goalEquity: 0,
            investimentTime: { value: 1, option: 'Meses' },
            optionsProfitability: [
                { name: 'a.m.', code: 'a.m.' },
                { name: 'a.a.', code: 'a.a.' }
            ],
            profitability: {
                value: 0,
                option: { name: 'a.m.', code: 'a.m.' }
            },
            optionsTime: ['Meses', 'Anos'],
            resultSimulation: 0
        };
    },
    methods: {
        calculate() {
            const investimentTime = this.investimentTime.option === 'Meses' ? this.investimentTime.value : this.investimentTime.value * 12;
            const profitability = this.profitability.option.name === 'a.m.' ? (this.profitability.value as number) / 100 : this.convertTaxeToMonthly(this.profitability.value as number);
            this.resultSimulation = (this.goalEquity as number) / ((Math.pow(1 + profitability, investimentTime) - 1) / profitability);
        },
        clearFields() {
            this.goalEquity = 0;
            this.investimentTime.value = 0;
            this.investimentTime.option = 'Meses';
            this.profitability = {
                value: 0,
                option: { name: 'a.m.', code: 'a.m.' }
            };
            this.resultSimulation = 0;
        },
        convertTaxeToMonthly(taxe: number) {
            return Math.pow(1 + taxe / 100, 1 / 12) - 1;
        }
    },
    setup() {
        return { formatCurrency };
    }
};
</script>

<template>
    <div className="card">
        <h4>Simulador de Aporte Mensal</h4>
        <p>O resultado mostrará quanto você deverá investir mensalmente a fim de atingir um patrimônio "alvo" atribuído por você.</p>
        <div className="p-fluid formgrid grid">
            <div className="field col-12 md:col-4">
                <label>Patrimônio "alvo"</label>
                <InputGroup>
                    <InputGroupAddon>R$</InputGroupAddon>
                    <MInputNumber v-model="goalEquity" />
                </InputGroup>
            </div>
            <div className="field col-12 md:col-4">
                <label>Prazo</label>
                <InputGroup>
                    <InputNumber v-model="investimentTime.value" />
                    <SelectButton v-model="investimentTime.option" :options="optionsTime" aria-labelledby="basic" />
                </InputGroup>
            </div>
            <div className="field col-12 md:col-4">
                <label>Rentabilidade <i class="pi pi-question-circle" v-tooltip.focus.top="'Taxa de rentabilidade onde o dinheiro será aplicado.'" tabindex="2"></i></label>
                <InputGroup>
                    <MInputNumber v-model="profitability.value" />
                    <InputGroupAddon>%</InputGroupAddon>
                    <Dropdown v-model="profitability.option" :options="optionsProfitability" optionLabel="name" class="w-7rem" />
                </InputGroup>
            </div>
            <div className="field col-12 mt-3">
                <div class="flex flex-wrap sm:flex-nowrap" style="max-width: 500px">
                    <Button label="Calcular" @click="calculate" />
                    <Button label="Limpar" @click="clearFields" link />
                </div>
            </div>
        </div>
        <div className="col-12 mt-5 result" v-if="resultSimulation">
            <h4>Resultado</h4>
            <div class="grid mt-3">
                <div class="col-12 md:col-6 md:col-offset-3 xl:col-4 xl:col-offset-4">
                    <Fieldset legend="Investimento mensal">
                        <h5 class="positive">{{ formatCurrency(resultSimulation) }}</h5>
                    </Fieldset>
                </div>
            </div>
        </div>
    </div>
    <MainAdsenseBottom />
</template>

<style scoped lang="scss">
.result {
    .grid {
        h5 {
            text-align: center;
            margin-bottom: 0;
            font-size: 24px;

            &.positive {
                color: var(--green-700);
            }
        }
    }
}
</style>
