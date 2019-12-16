<template>
    <div>
        <div class="crumbs">
            <el-breadcrumb separator="/">
                <el-breadcrumb-item><i class="el-icon-lx-search"></i> Report Management</el-breadcrumb-item>
                <el-breadcrumb-item>Report</el-breadcrumb-item>
                <el-breadcrumb-item>Send</el-breadcrumb-item>
            </el-breadcrumb>
        </div>
        <div class="container">
            <div class="form-box">
                <el-form ref="form" :model="form" label-width="120px">
                    <el-form-item label="Order ID">
                        <el-input v-model="form.order"></el-input>
                    </el-form-item>
                    <el-form-item label="Target User">
                        <el-input v-model="form.target"></el-input>
                    </el-form-item>
                    <el-form-item label="Reason">
                        <el-input type="textarea" rows="5" v-model="form.reason"></el-input>
                    </el-form-item>
                    <el-form-item>
                        <el-button type="primary" @click="onSubmit">Submit</el-button>
                        <el-button>Cancel</el-button>
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
                    order: '',
                    target: '',
                    reason: ''
                }
            }
        },
        methods: {
            onSubmit() {
                this.$axios.post("/service/arbitration/report", {
                    from: localStorage.getItem('ms_username'),
                    order: this.form.order,
                    target: this.form.target,
                    reason: this.form.reason
                }).then(res => {
                    if (res.data.result) {
                        this.$message.success('Success!');
                    }
                    else {
                        this.$message.error('User or admin not found!');
                    }
                }).catch(err => {
                    this.$message.error('Error!');
                })
            }
        }
    }
</script>