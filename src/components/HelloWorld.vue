<template>
    <el-cascader-panel   :props="props"></el-cascader-panel>
</template>

<script>
    export default {
        name: 'HelloWorld',
        methods: {

            getLevel: function (level) {
                if (level == 'province')
                    return 2;
                if (level == 'city') return 3;
                if (level == 'district') return 4;
            },
            getData: function (fid, level) {
                return window.axios.get(`http://localhost:9600/v1/api/xzhf?fid=${fid}&level=${level}`)

            }
        },
        data() {
            return {
                props: {
                    lazy: true,
                    lazyLoad(node, resolve) {

                        if (node.level == 4) {
                            resolve([])
                            return
                        }
                        let parentId = ''
                        if (node.level == 0) {
                            parentId = 100000
                        } else {
                            parentId = node.value
                        }

                        window.axios.get(`http://localhost:9600/v1/api/xzhf?fid=${parentId}&level=${node.level + 1}`).then(data => {
                            if (data.data.result) {

                                resolve([{
                                    name: '数据补充中',
                                    adcode: '2',
                                    leaf: true
                                }])
                                return
                            }
                            const nodes = data.data
                                .map(item => ({
                                    adcode: item.adcode,
                                    name: item.name,
                                    leaf: node.level >= 3
                                }))
                            ;
                            resolve(nodes)
                        })
                    },
                    leaf: 'parent',
                    label: 'name',
                    value: 'adcode'
                },
                options: []
            };
        },

        mounted() {
            document.title = '城市级联'
        }

    }
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->

<style scoped>
    h3 {
        margin: 40px 0 0;
    }

    ul {
        list-style-type: none;
        padding: 0;
    }

    li {
        display: inline-block;
        margin: 0 10px;
    }

    a {
        color: #42b983;
    }
</style>
