<template>
    <div>
        <div class="crumbs">
            <el-breadcrumb separator="/">
                <el-breadcrumb-item><i class="el-icon-lx-qrcode"></i> Data Management</el-breadcrumb-item>
                <el-breadcrumb-item>Query</el-breadcrumb-item>
                <el-breadcrumb-item>Single</el-breadcrumb-item>
            </el-breadcrumb>
        </div>
        <div class="container">
            <div class="form-box">
                <el-form ref="form" :model="form" label-width="80px">
                    <el-form-item label="Data ID">
                        <el-input v-model="form.id"></el-input>
                    </el-form-item>
                    <el-form-item>
                        <el-button type="primary" @click="onSubmit">Submit</el-button>
                        <el-button>Cancel</el-button>
                    </el-form-item>
                </el-form>
            </div>
        </div>

        <div class="container">
            <div class="form-box">
                <el-form ref="form" label-width="80px">
                    <el-form-item label="Result">
                        <el-table :data="results" border class="table" ref="multipleTable">
                            <el-table-column prop="id" label="ID" sortable>
                            </el-table-column>
                            <el-table-column prop="name" label="Property Name">
                            </el-table-column>
                            <el-table-column prop="data" label="Data">
                            </el-table-column>
                            <el-table-column prop="status" label="Status">
                            </el-table-column>
                        </el-table>
                    </el-form-item>
                </el-form>
            </div>
        </div>

    </div>
</template>

<script>
    export default {
        name: 'baseform',
        data: function(){
            return {
                form: {
                    id: ''
                },
                properties:[],
                results: []
            }
        },
        mounted(){
            this.$axios.get("/service/system/getPropertyNames")
                .then(res => {
                    this.properties = res.data;
                });
        },
        methods: {
            onSubmit() {
                this.$axios.post("/service/data/readMultiple", {
                    ids: [this.form.id],
                    propertyNames: this.properties
                }).then(res => {
                    var temp = [];
                    for(var property in res.data.data) {
                        var single = res.data.data[property];
                        for (var id in single) {
                            temp.push({
                                id: id,
                                name: property,
                                data: single[id].data,
                                status: single[id].status
                            });
                        }
                    }
                    this.results = temp;
                }).catch(err => {
                    this.$message.error('Error!');
                })
            }
        }
    }
</script>