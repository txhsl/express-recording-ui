<template>
    <div class="table">
        <div class="crumbs">
            <el-breadcrumb separator="/">
                <el-breadcrumb-item><i class="el-icon-lx-group"></i> Data Management</el-breadcrumb-item>
                <el-breadcrumb-item>Orders</el-breadcrumb-item>
            </el-breadcrumb>
        </div>
        <div class="container">
            <div class="handle-box">
                <el-select v-model="select_status" placeholder="Status" class="handle-select mr10">
                    <el-option key="1" label="" value=""></el-option>
                    <el-option key="2" label="Ordered" value="Ordered"></el-option>
                    <el-option key="3" label="Picked" value="Picked"></el-option>
                    <el-option key="4" label="Received" value="Received"></el-option>
                </el-select>
                <el-input v-model="select_word" placeholder="Keyword" class="handle-input mr10"></el-input>
                <el-button type="primary" @click="handleQR()"><i class="el-icon-lx-search"></i> QR Code</el-button>

                <div style="float:right">
                    <el-button type="primary" @click="handleNew()"><i class="el-icon-lx-add"></i> New</el-button>
                    <el-button type="primary" @click="getData()"><i class="el-icon-lx-refresh"></i> Refresh</el-button>
                </div>
            </div>
            <el-table :data="data" border class="table" ref="multipleTable" @selection-change="handleSelectionChange">
                <el-table-column type="expand">
                    <template slot-scope="props">
                        <el-form label-position="left" inline class="order-table-expand">
                            <el-form-item label="Order ID">
                                <span>{{ props.row.id }}</span>
                            </el-form-item>
                            <el-form-item label="Photo">
                                <span>{{ props.row.imgQR }}</span>
                                <el-button type="text" @click="display(props.row.imgQR)"><i class="el-icon-lx-attention"></i></el-button>
                            </el-form-item>
                            <el-form-item label="Value(EC)">
                                <span>{{ props.row.value }}</span>
                            </el-form-item>
                            <el-form-item label="Sender">
                                <span>{{ props.row.sender }}</span>
                            </el-form-item>
                            <el-form-item label="Sender's Phone">
                                <span>{{ props.row.senderPhone }}</span>
                            </el-form-item>
                            <el-form-item label="Sender's Address">
                                <span>{{ props.row.senderAddress }}</span>
                            </el-form-item>
                            <el-form-item label="Receiver">
                                <span>{{ props.row.receiver }}</span>
                            </el-form-item>
                            <el-form-item label="Receiver's Phone">
                                <span>{{ props.row.receiverPhone }}</span>
                            </el-form-item>
                            <el-form-item label="Receiver's Address">
                                <span>{{ props.row.receiverAddress }}</span>
                            </el-form-item>
                            <el-form-item label="Delivery Date">
                                <span>{{ props.row.date }}</span>
                            </el-form-item>
                            <el-form-item label="Deliverer">
                                <span>{{ props.row.deliverer }}</span>
                            </el-form-item>
                            <el-form-item label="Weight(KG)">
                                <span>{{ props.row.weight }}</span>
                            </el-form-item>
                            <el-form-item label="Fee(EC)">
                                <span>{{ props.row.fee }}</span>
                            </el-form-item>
                            <el-form-item label="Ordered">
                                <span>{{ props.row.ordered }}</span>
                            </el-form-item>
                            <el-form-item label="Picked">
                                <span>{{ props.row.pickedUp }}</span>
                            </el-form-item>
                            <el-form-item label="Delivering">
                                <span>{{ props.row.delivering }}</span>
                            </el-form-item>
                            <el-form-item label="Received">
                                <span>{{ props.row.received }}</span>
                            </el-form-item>
                            <el-form-item>
                                <el-button type="primary" @click="handlePick(props.$index, props.row)" :disabled="checkPicked(props.$index, props.row)">Pick</el-button>
                                <el-button type="primary" @click="handleDeliver(props.$index, props.row)" :disabled="checkDelivered(props.$index, props.row)">Start Deliver</el-button>
                                <el-button type="primary" @click="handleReceive(props.$index, props.row)" :disabled="checkReceived(props.$index, props.row)">Confirm to Receive</el-button>
                            </el-form-item>
                        </el-form>
                    </template>
                </el-table-column>
                <el-table-column prop="id" label="Order ID" sortable width="240">
                </el-table-column>
                <el-table-column prop="value" label="Value(EC)" width="120">
                </el-table-column>
                <el-table-column prop="senderAddress" label="Sender's Address">
                </el-table-column>
                <el-table-column prop="receiverAddress" label="Receiver's Address">
                </el-table-column>
                <el-table-column prop="date" label="Delivery Date">
                </el-table-column>
                <el-table-column label="Status" width="120">
                    <template slot-scope="scope">
                        <span>{{ getStatus(scope.row) }}</span>
                    </template>
                </el-table-column>
            </el-table>
            <div class="pagination">
                <el-pagination background @current-change="handleCurrentChange" layout="prev, pager, next" :total="calculPage()">
                </el-pagination>
            </div>
        </div>

        <!-- 新建表单 -->
        <el-dialog title="Create Order" :visible.sync="createVisible" width="40%">
            <el-form ref="form" :model="create" label-width="140px">
                <el-form-item label="Photo">
                    <el-input v-model="create.imgQR" disabled></el-input>
                </el-form-item>
                <el-form-item>
                    <el-upload
                        name="img"
                        class="avatar-uploader"
                        action="/service/app/upload"
                        :show-file-list="false"
                        :on-success="onSuccess"
                        :on-error="onFailure"
                        :before-upload="beforeAvatarUpload">
                        <img v-if="create.imgQR" :src="getUrl(create.imgQR)" class="avatar">
                        <i v-else class="el-icon-plus avatar-uploader-icon"></i>
                    </el-upload>
                </el-form-item>
                <el-form-item label="Value">
                    <el-input v-model="create.value" placeholder="EC"></el-input>
                </el-form-item>
                <el-form-item label="Sender">
                    <el-input v-model="create.sender" disabled></el-input>
                </el-form-item>
                <el-form-item label="Sender's Phone">
                    <el-input v-model="create.senderPhone" placeholder="+86"></el-input>
                </el-form-item>
                <el-form-item label="Sender's Address">
                    <el-input v-model="create.senderAddress"></el-input>
                </el-form-item>
                <el-form-item label="Receiver">
                    <el-input v-model="create.receiver"></el-input>
                </el-form-item>
                <el-form-item label="Receiver's Phone">
                    <el-input v-model="create.receiverPhone" placeholder="+86"></el-input>
                </el-form-item>
                <el-form-item label="Receiver's Address">
                    <el-input v-model="create.receiverAddress"></el-input>
                </el-form-item>
                <el-form-item label="Delivery Date">
                    <el-date-picker v-model="create.date" type="date"></el-date-picker>
                </el-form-item>
            </el-form>

            <span slot="footer" class="dialog-footer">
                <el-button @click="createVisible = false">Cancel</el-button>
                <el-button type="primary" @click="applyNew()">Confirm</el-button>
            </span>

        </el-dialog>

        <!-- 取件表单 -->
        <el-dialog title="Deliver Order" :visible.sync="deliverVisible" width="40%">
            <el-form ref="form" :model="deliver" label-width="140px">
                <el-form-item label="Deliverer">
                    <el-input v-model="deliver.deliverer" disabled></el-input>
                </el-form-item>
                <el-form-item label="Weight">
                    <el-input v-model="deliver.weight" placeholder="kg"></el-input>
                </el-form-item>
            </el-form>

            <span slot="footer" class="dialog-footer">
                <el-button @click="deliverVisible = false">Cancel</el-button>
                <el-button type="primary" @click="applyDeliver()">Confirm</el-button>
            </span>

        </el-dialog>

        <!-- 二维码扫描框 -->
        <el-dialog title="QR Scanner" :visible.sync="qrVisible" width="40%">
            <el-form ref="form" :model="qr" label-width="140px">
                <qrcode-stream :camera="qr.camera" @decode="onDecode" @init="onInit">
                    <div v-if="validationSuccess" class="validation-success">QR code validated</div>
                    <div v-if="validationFailure" class="validation-failure">Invalid QR code</div>
                    <div v-if="validationPending" class="validation-pending">Validation in progress...</div>
                </qrcode-stream>
            </el-form>

            <span slot="footer" class="dialog-footer">
                <el-button @click="qrVisible = false; turnCameraOff()">Cancel</el-button>
            </span>

        </el-dialog>

        <!-- 接单提示框 -->
        <el-dialog title="Warning" :visible.sync="pickVisible" width="300px" center>
            <div class="del-dialog-cnt">Pick this order?</div>
            <span slot="footer" class="dialog-footer">
                <el-button @click="pickVisible = false">No</el-button>
                <el-button type="primary" @click="applyPick()">Yes</el-button>
            </span>
        </el-dialog>

        <!-- 收件提示框 -->
        <el-dialog title="Warning" :visible.sync="receiveVisible" width="300px" center>
            <div class="del-dialog-cnt">Confirm to receive?</div>
            <span slot="footer" class="dialog-footer">
                <el-button @click="receiveVisible = false">No</el-button>
                <el-button type="primary" @click="applyReceive()">Yes</el-button>
            </span>
        </el-dialog>

        <!-- 订单成功提示框 -->
        <el-dialog title="Success" :visible.sync="successVisible" width="300px" center>
            <div class="del-dialog-cnt">
                <qrcode :value="qrResult"></qrcode>
            </div>
            <span slot="footer" class="dialog-footer">
                <el-button @click="successVisible = false">Confirm</el-button>
            </span>
        </el-dialog>

        <!-- 照片 -->
        <el-dialog title="Photo" :visible.sync="picVisible" width="300px" center>
            <div class="del-dialog-cnt">
                <img :src="current_pic" class="avatar">
            </div>
            <span slot="footer" class="dialog-footer">
                <el-button @click="picVisible = false">Confirm</el-button>
            </span>
        </el-dialog>
    </div>
</template>

<script>
    import { QrcodeStream } from 'vue-qrcode-reader'

    export default {
        components: { QrcodeStream },
        name: 'basetable',
        data() {
            return {
                name: localStorage.getItem('ms_username'),
                tableData: [],
                sortedData: [],
                cur_page: 1,
                row_per_page: 10,
                multipleSelection: [],
                select_cate: '',
                select_status: '',
                select_word: '',
                del_list: [],
                createVisible: false,
                pickVisible: false,
                deliverVisible: false,
                receiveVisible: false,
                qrVisible: false,
                successVisible: false,
                picVisible: false,
                idx: -1,
                select_row: {},
                current_pic: '',
                create: {
                    imgQR: '',
                    value: '',
                    sender: '',
                    senderPhone: '',
                    senderAddress: '',
                    receiver: '',
                    receiverPhone: '',
                    receiverAddress: '',
                    date: '',
                    imgData: ''
                },
                deliver: {
                    weight: '',
                    deliverer: ''
                },
                qr: {
                    isValid: undefined,
                    camera: 'auto',
                    result: null
                },
                qrResult: ''
            }
        },
        created() {
            this.getData();
        },
        computed: {
            data() {
                this.sortedData = this.tableData.filter((d) => {
                    let is_del = false;
                    for (let i = 0; i < this.del_list.length; i++) {
                        if (d.id === this.del_list[i].id) {
                            is_del = true;
                            break;
                        }
                    }
                    if (!is_del) {
                        if (d.sender.indexOf(this.select_word) > -1 ||
                                d.senderPhone.indexOf(this.select_word) > -1 ||
                                d.senderAddress.indexOf(this.select_word) > -1 ||
                                d.receiver.indexOf(this.select_word) > -1 ||
                                d.receiverPhone.indexOf(this.select_word) > -1 ||
                                d.receiverAddress.indexOf(this.select_word) > -1 ||
                                d.id.indexOf(this.select_word) > -1 ||
                                d.date.indexOf(this.select_word) > -1
                        ) {
                            switch(this.select_status) {
                                case 'Ordered':
                                    if (d.ordered == 'Yes') {
                                        return d;
                                    }
                                case 'Picked':
                                    if (d.pickedUp == 'Yes') {
                                        return d;
                                    }
                                case 'Received':
                                    if (d.received == 'Yes') {
                                        return d;
                                    }
                                default:
                                    return d;
                            }
                        }
                    }
                })

                return this.sortedData.slice(this.row_per_page*(this.cur_page-1), this.row_per_page*this.cur_page);
            },
            validationPending () {
                return this.qr.isValid === undefined
                    && this.qr.camera === 'off'
            },
            validationSuccess () {
                return this.qr.isValid === true
            },
            validationFailure () {
                return this.qr.isValid === false
            }
        },
        methods: {
            // 分页导航
            handleCurrentChange(val) {
                this.cur_page = val;
            },
            getData() {
                var data = [];
                var owned = [];
                var managed = [];
                this.cur_page = 1;
                this.$axios.get("/service/app/all")
                    .then(res => {
                        this.tableData = res.data;
                    }).catch(err => {
                        this.$message.error('Error!');
                    });
            },
            handleNew() {
                this.createVisible = true;
                this.create.sender = this.name;
            },
            handlePick(index, row) {
                this.idx = index;
                this.select_row = row;
                this.pickVisible = true;
            },
            handleDeliver(index, row) {
                this.idx = index;
                this.select_row = row;
                this.deliver.deliverer = this.name;
                this.deliverVisible = true;
            },
            handleReceive(index, row) {
                this.idx = index;
                this.select_row = row;
                this.receiveVisible = true;
            },
            handleQR() {
                this.qrVisible = true;
                this.turnCameraOn();
            },
            applyNew(){
                this.$axios.post('/service/app/order', this.create).then(res => {
                    this.$message.success('Order requested!');
                    this.qrResult = res.data;
                    this.successVisible = true;
                }).catch(err => {
                    this.$message.error('Error!');
                })
                this.createVisible = false;
            },
            applyPick(){
                this.$axios.post('/service/app/pickUp', {
                    id: this.select_row.id
                }).then(res => {
                    this.$message.success('Pick Requested!');
                }).catch(err => {
                    this.$message.error('Error!');
                })
                this.pickVisible = false;
            },
            applyDeliver(){
                this.$axios.post('/service/app/deliver', {
                    id: this.select_row.id,
                    weight: this.deliver.weight
                }).then(res => {
                    this.$message.success('Deliver requested! The fee is ' + res.data + ' EC.');
                }).catch(err => {
                    this.$message.error('Error!');
                })
                this.deliverVisible = false;
            },
            applyReceive(){
                this.$axios.post('/service/app/receive', {
                    id: this.select_row.id
                }).then(res => {
                    this.$message.success('Receive Requested!');
                }).catch(err => {
                    this.$message.error('Error!');
                })
                this.receiveVisible = false;
            },
            handleSelectionChange(val) {
                this.multipleSelection = val;
            },
            checkPicked(index, row) {
                return row.pickedUp === 'Yes';
            },
            checkDelivered(index, row) {
                return row.delivering === 'Yes';
            },
            checkReceived(index, row) {
                return row.received === 'Yes';
            },
            calculPage() {
                return this.sortedData.length;
            },
            getStatus(order) {
                if (order.received === 'Yes') {
                    return 'Received';
                }
                else if (order.delivering === 'Yes') {
                    return 'Delivering';
                }
                else if (order.picked === 'Yes') {
                    return 'Picked';
                }
                else {
                    return 'Ordered';
                }
            },

            onSuccess(res, file, fileList) {
                this.create.imgQR = res;
            },
            onFailure(err, file, fileList) {
                this.$message.error('Upload failed!');
            },
            getUrl(hash) {
                return '/service/app/download/' + hash;
            },
            beforeAvatarUpload(file) {
                const isJPG = file.type === 'image/jpeg';
                const isLt2M = file.size / 1024 / 1024 < 2;

                if (!isJPG) {
                    this.$message.error('Only JPG supported!');
                }
                if (!isLt2M) {
                    this.$message.error('Image size exceeds 2MB!');
                }
                return isJPG && isLt2M;
            },
            display(hash) {
                this.picVisible = true;
                this.current_pic = this.getUrl(hash);
            },

            onInit (promise) {
                promise
                    .catch(error => {
                        if (error.name === 'NotAllowedError') {
                            this.$message.error("Camera access permisson needed");
                        } else if (error.name === 'NotFoundError') {
                            this.$message.error("No camera on this device");
                        } else if (error.name === 'NotSupportedError') {
                            this.$message.error("Secure context required (HTTPS, localhost)");
                        } else if (error.name === 'NotReadableError') {
                            this.$message.error("Camera already in use");
                        } else if (error.name === 'OverconstrainedError') {
                            this.$message.error("Installed cameras are not suitable");
                        } else if (error.name === 'StreamApiNotSupportedError') {
                            this.$message.error("Stream API is not supported in this browser");
                        }
                    })
                    .then(this.resetValidationState)
            },
            resetValidationState () {
                this.qr.isValid = undefined;
            },
            async onDecode (content) {
                this.qr.result = content;
                this.select_word = content;
                this.turnCameraOff();

                await this.timeout(1000);
                this.qr.isValid = content.startsWith('');

                await this.timeout(1000);
                this.qrVisible = false;
            },
            turnCameraOn () {
                this.qr.camera = 'auto'
            },
            turnCameraOff () {
                this.qr.camera = 'off'
            },
            timeout (ms) {
                return new Promise(resolve => {
                    window.setTimeout(resolve, ms)
                })
            }
        }
    }

</script>

<style scoped>
    .handle-box {
        margin-bottom: 20px;
    }

    .handle-select {
        width: 120px;
    }

    .handle-input {
        width: 300px;
        display: inline-block;
    }
    .del-dialog-cnt{
        font-size: 16px;
        text-align: center
    }
    .table{
        width: 100%;
        font-size: 14px;
    }
    .red{
        color: #ff0000;
    }
    .mr10{
        margin-right: 10px;
    }
    .order-table-expand {
        font-size: 0;
    }
    .order-table-expand span {
        color: #99a9bf;
    }
    .order-table-expand .el-form-item {
        margin-right: 0;
        margin-bottom: 0;
        width: 50%;
    }
    .validation-success,
    .validation-failure,
    .validation-pending {
        position: absolute;
        width: 100%;
        height: 100%;

        background-color: rgba(255, 255, 255, .8);
        text-align: center;
        font-weight: bold;
        font-size: 1.4rem;
        padding: 10px;

        display: flex;
        flex-flow: column nowrap;
        justify-content: center;
    }
    .validation-success {
        color: green;
    }
    .validation-failure {
        color: red;
    }
    .avatar-uploader .el-upload {
        border: 1px dashed #d9d9d9;
        border-radius: 6px;
        cursor: pointer;
        position: relative;
        overflow: hidden;
    }
    .avatar-uploader .el-upload:hover {
        border-color: #409EFF;
    }
    .avatar-uploader-icon {
        font-size: 28px;
        color: #8c939d;
        width: 178px;
        height: 178px;
        line-height: 178px;
        text-align: center;
    }
    .avatar {
        width: 178px;
        height: 178px;
        display: block;
    }
</style>
