<template>
    <div class="onboarding">
        <div class="content">
            <i class="step-image" :class="this.steps[this.currentStep].image_class"></i>
            <h2 class="title" v-html="this.steps[this.currentStep].title"></h2>
            <p class="description" v-html="this.steps[this.currentStep].content"></p>
            <div v-if="currentStep === 1"
                 class="download">
                <p v-if="isDownload">
                    Your key was saved on:
                    {{ isDownload }}
                </p>
                <button @click="backupKey"
                        class="button primary is-small">Click here to download
                </button>
            </div>
        </div>
        <div class="navigation"
             v-if="currentStep !== 0 && isNextStepEnabled">
            <a class="skip"
               @click="skipOnboardingSteps">
                <span>Skip</span>
            </a>
            <a class="next"
               @click="navigateToNextStep()">
                <span>Next</span>
                <span class="arrow arrow-right"></span>
            </a>
            <a class="previous"
               @click="navigateToPreviousStep()">
                <span class="arrow arrow-left"></span>
                <span>Previous</span>
            </a>
        </div>
        <div v-if="currentStep === 0">
            <span class="button primary"
                  @click="navigateToNextStep()">
                Lets go!
            </span>
        </div>
        <div class="navigation last"
             v-if="!isNextStepEnabled">
            <span class="button primary" @click="skipOnboardingSteps()">
                Let’s upload!
            </span>
            <a class="previous"
               :disabled="isPreviousStepEnabled"
               @click="navigateToPreviousStep()">
                <span class="arrow arrow-left"></span>
                <span>Previous</span>
            </a>
        </div>
    </div>
</template>

<script>
    import Router from '@/router';
    import {mapGetters} from 'vuex';
    import Api from '../services/api';

    export default {
        name: 'Onboarding',
        data() {
            return {
                isDownload: null,
                currentStep: 0,
                steps: [
                    {
                        slot: 'step1',
                        image_class: 'step1',
                        title: 'Introduction',
                        content: '<p>Thank you for installing BitDust.</p><p>We will run you through a couple of important steps.</p>'
                    },
                    {
                        slot: 'step2',
                        image_class: 'step2',
                        title: 'Private Key',
                        content: `<p>Every file you upload in the BitDust network is securely encrypted using a private key. A private key is a unique 256-bit number that is paired with a public key to set off algorithms for data
                                    encryption and decryption. It provides a secure way to access your account and upload/share files, making sure that you are the only one that can access your data. Never share your private key with
                                    any person or software that you do not intend to take control over your files. In addition it is important to back up your private key so you can retrieve your identity in the future.</p>
                                    <p>Please store the back up of your private key in a secure place.</p>`
                    },
                    {
                        slot: 'step3',
                        image_class: 'step3',
                        title: 'Storage',
                        content: `<p>Storage in the BitDust network is provided by numerous people, called suppliers, who rent out their free hard disk space to customers like you.
                                    BitDust automatically selects a couple of suppliers at random which are called your family.When you upload a file to the BitDust network your family of suppliers will store your data.
                                    Off course you can also manually select your family members and tweak other settings, providing you with full control over your data. To ensure redundancy every file you upload is copied twice.
                                    For example if you upload 5 mb of pictures you are actually using 10 mb (2X5Mb) to store your pictures.</p>`
                    },
                    {
                        slot: 'step4',
                        image_class: 'step5',
                        title: 'Upload Your First File',
                        content: `<p>We will now show you how you can upload your first file. In addition we will run you through other features.</p>`
                    }
                ]
            };
        },
        created() {
            document.getElementsByTagName('html')[0].classList.add('intro-background');
        },
        computed: {
            ...mapGetters([
                'connectionStatus'
            ]),
            isNextStepEnabled() {
                return this.currentStep !== this.steps.length - 1;
            },
            isPreviousStepEnabled() {
                return this.currentStep !== 0;
            }
        },
        methods: {
            async backupKey() {
                Api.identityBackup();
                const {result} = await Api.getPath();
                this.isDownload = result[0].value;
            },
            skipOnboardingSteps() {
                Router.push('/files');
            },
            navigateToNextStep() {
                if (this.currentStep < this.steps.length - 1) {
                    this.currentStep++;
                }
            },
            navigateToPreviousStep() {
                if (this.currentStep > 0) {
                    this.currentStep--;
                }
            }
        }
    };
</script>

<style lang="scss" scoped>
    @import "../assets/scss/includes.scss";

    .onboarding {
        display: block;
        width: 600px;
        margin: 60px auto;
        background: $color-white;
        text-align: center;
        padding: 40px 0;
        color: $color-gray-1;

        .description {
            font-size: 1rem;
            margin: 20px auto;
            max-width: 80%;

            /deep/ textarea {
                width: 85%;
                height: 100px;
                margin: 15px;
                padding: 15px;
                max-width: 100%;
                min-width: 100%;
            }
        }
    }

    .title {
        @include metric;
    }

    .button {
        display: inline-block;
        margin: 20px auto;
        max-width: 300px;

        .download & {
            padding: 16px;
            font-size: 1.2rem;
        }
    }

    .download {
        p {
            font-size: 1rem;
            color: $color-red;
        }
    }

    .step-image {
        display: block;
        width: 140px;
        height: 140px;
        background-repeat: no-repeat;
        background-size: contain;
        margin: 40px auto;
    }

    .navigation {
        font-size: 1rem;
        color: $color-purple-1;

        &.last {
            display: flex;
            flex-direction: column;
            justify-content: center;
        }

        a {
            cursor: pointer;
            font-weight: bold;

            &:hover {
                span {
                    border-color: $color-purple-1;
                    color: $color-purple-1;
                    font-weight: bold;
                }
            }
        }

        .skip {
            float: left;
            margin-left: 25px;
        }

        .previous, .next {
            float: right;
            margin-right: 25px;
        }

        .arrow {
            border-bottom: 1px solid $color-gray-1;
            border-right: 1px solid $color-gray-1;
            width: 8px;
            height: 8px;
            display: inline-block;
            position: relative;
            top: -1px;
        }

        .arrow-left {
            transform: rotate(135deg);
        }

        .arrow-right {
            transform: rotate(315deg)
        }
    }

    .step1 {
        background-image: url('../assets/icons/onboarding/step1.svg');
    }

    .step2 {
        background-image: url('../assets/icons/onboarding/step2.svg');
    }

    .step3 {
        background-image: url('../assets/icons/onboarding/step3.svg');
    }

    .step4 {
        background-image: url('../assets/icons/onboarding/step4.svg');
    }

    .step5 {
        background-image: url('../assets/icons/onboarding/step5.svg');
    }
</style>