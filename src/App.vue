<template>
    <v-app>
        <v-app-bar app color="primary" dark>
            <v-toolbar-title>Bus Tracker</v-toolbar-title>
        </v-app-bar>
        <v-content class="mx-2">
            <v-row class="justify-center">
                <v-col cols="12" xl="6" lg="8" md="10" sm="12" xs="12">
                    <v-data-table v-if="dataReady" :loading="!dataReady" loading-text="Nothing here :("
                                  class="elevation-1"
                                  :headers="headers" :items-per-page="10"
                                  :items="services">
                    </v-data-table>
                </v-col>
            </v-row>
        </v-content>
    </v-app>
</template>

<script>
    import axios from 'axios';

    require('typeface-open-sans');

    export default {
        name: 'App',
        data() {
            return {
                dataReady: false,
                headers: [
                    {text: 'Linha', align: 'center', value: 'serviceMnemonic'}
                ],
                services: [],
                editedItem: Object.assign({}, this.defaultItem),
                defaultItem: {
                    routeCode: '',
                    routeMnemonic: '',
                    companyId: 0,
                    stopId: 0,
                    serviceId: 0,
                    serviceMnemonic: '',
                    predictionType: '',
                    activeVehicles: 0,
                    scheduledVehicles: 0,
                    messages: [],
                    vehicles: [],
                    hasRealTime: false
                },
                defaultVehicle: {
                    plate: '',
                    prefix: '',
                    wheelchair: false,
                    climatized: false,
                    prediction: Infinity,
                    age: 0,
                    type: ''
                },
                url: {
                    base: 'https://api.cittamobi.com.br/m3p/js/',
                    busStop: 'prediction/stop/',
                    line: 'vehicles/service/'
                },
                code: {
                    line: {
                        '270': {'C': '8508022', 'B': '8508023'}
                    },
                    busStop: {
                        'PUCRS Bento C': '5056725'
                    }
                }
            };
        },
        methods: {
            validateStatus(status) {
                return status === '' || status === 304 || (status >= 200 && status < 300);
            },
            axiosError(error) {
                if (error.response) {
                    alert(error.data);
                } else if (error.request) {
                    alert('No response from server.');
                } else {
                    alert(error.message);
                }
            }
        },
        mounted() {
            axios.get(this.url.base + this.url.busStop + this.code.busStop['PUCRS Bento C'], {validateStatus: this.validateStatus})
                .then((response) => {
                    console.log(response);
                    this.services = response.data.services;
                    this.this.dataReady = true;
                })
                .catch(this.axiosError);
        }
    };
</script>

<style lang="scss">
    html {
        font-family: 'Open Sans';
    }

    .top-navbar {
        .vs-navbar {
            height: 60px;
            position: fixed;
            top: 0;
            width: 100%;
        }

        main {
            inset: 60px 0 0;
            position: relative;
        }
    }
</style>