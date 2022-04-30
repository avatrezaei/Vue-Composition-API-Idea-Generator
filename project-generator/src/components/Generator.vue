<template>
    <div class="generator">
        <section class="hero is-primary is-bold has-text-centered py-6">
            <div class="hero-body">
                <div class="container">
                    <h1 class="title">
                        What skills do you want to work on?
                    </h1>
                    <div class="columns">
                        <div v-for="skill in skillList" :key="skill.id" class="column">
                            <div class="field">
                                <input :id="skill.id"
                                       type="checkbox"
                                       :name="skill.id"
                                       class="switch is-danger is-rounded"
                                       v-model="selectedSkills"
                                       @change="generateFilteredAppList"
                                       :value="skill.id"
                                >
                                <label :for="skill.id">{{ skill.skill }}</label>
                            </div>
                        </div>
                    </div>

                </div>
            </div>
        </section>

        <!-- 👇 new code -->
        <div class="container">
            <div class="columns is-multiline mt-3">
                <div v-for="app in filteredAppList" :key="app.id" class="column is-one-third">
                    <div class="card is-paddingless">
                        <div class="row">
                            <div class="media">
                                <div class="media-right">
                                    <figure class="image is-64x64">
                                        <img class="is-rounded" src=
                                                     "https://media.geeksforgeeks.org/wp-content/uploads/20200611151025/gfg202.png"
                                             alt="Placeholder image">
                                    </figure>
                                </div>

                                <div class="media-right">
                                    <p class="card-header-title is-uppercase is-size-5">
                                        {{ app.app }}
                                    </p>
                                </div>
                            </div>

                        </div>

                        <div class="card-content">
                            <div class="content has-text-left mb-4">
                                <p class="is-size-7">{{ app.instructions }}</p>
                                <h4>Skills:</h4>
                                <ul v-for="skill in app.skills" :key="skill.id">
                                    <li>
                                        <strong>{{ skillList[skill-1].skill }}</strong>
                                        <p v-if="skillList[skill-1].options" :set="randSkill = getRand(skillList[skill-1].options)">
                                            <a :href="randSkill">{{ randSkill }}</a>
                                        </p>
                                    </li>
                                </ul>
                            </div>
                        </div>
                    </div>
                </div>
            </div>
        </div>
        <!-- ☝️ end new code -->
    </div>
</template>

<script>
    import { ref } from 'vue';
    export default {
        name: 'project-generator',
        props: {},
        setup() {
            const GENERATOR_BASE = 'http://localhost:3000';
            const skillList = ref([]);
            const selectedSkills = ref([]);
            const filteredAppList = ref([]);
            let appList = [];

            // 👇 new code
            function generateFilteredAppList() {
                filteredAppList.value = [];

                for (const app of appList) {
                    const appSkillsArray = app.skills;
                    const selectedSkillsArray = selectedSkills.value;

                    if (hasAllSkills(appSkillsArray, selectedSkillsArray)) {
                        filteredAppList.value.push(app);
                    }
                }
            }

            // 👇 new code
            function hasAllSkills(appSkills, selectedSkills) {
                return selectedSkills.every(f => appSkills.includes(f));
            }

            async function getSkillList() {
                const response = await fetch(`${GENERATOR_BASE}/skills`);
                skillList.value = await response.json();
            }

            async function getAppList() {
                const response = await fetch(`${GENERATOR_BASE}/apps`);
                appList = await response.json();
                filteredAppList.value = appList;
            }

            // 👇 new code
            function getRand(value) {
                let keys = Object.keys(value);
                return value[keys[ keys.length * Math.random() << 0]];
            }


            getSkillList();
            getAppList();

            return {
                skillList,
                selectedSkills,
                filteredAppList,
                generateFilteredAppList,
                getRand
            }
        }

    }
</script>


<style scoped>
    label {
        font-size: 20px;
    }
    .card {
        height: 100%;
    }
    a {
        word-break: break-word;
    }
</style>
