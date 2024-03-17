<template>
    <div class="overlay" v-if="paymentSuccess" @click="closeSuccess">
        <div class="modal-success text-success">
            <div class="text-center success-icon">
                <span><i class="fa-solid fa-check"></i></span>
            </div>
            <h3 class="">Thanh toán thành công</h3>
        </div>
    </div>
    <div class="overlay" v-if="paymentSuccess ==false" @click="closeSuccess">
        <div class="modal-success text-danger">
            <div class="text-center success-icon">
                <span><i class="fa-solid fa-check"></i></span>
            </div>
            <h3 class="">Thanh toán không thành công</h3>
        </div>
    </div>
    <div class="admin-create-order">
        <h3>Thêm đơn hàng</h3>
        <form class="admin-form-order" ref="formOrder" @submit.prevent.stop="addOrder">
            <div class="row">
                <div class="group col-lg-4">
                    <div class="d-flex mb-1">
                        <label for="">Tên Khách hàng <span class="required">*</span></label>
                        <span class="btn btn-info btn-brand" @click="activeCustomer = true">Mới</span>
                    </div>
                    <!-- Them khách hàng mơi -->
                        <div class="overlay" v-if="activeCustomer">
                            <form action="" class="customer-form" @submit.prevent.stop="addCustomer">
                                <div class="close-form-customer" @click="closeFormCustomer">
                                    <i class="fa-solid fa-xmark"></i>
                                </div>
                                <div class="form-head">
                                    <h2 class="title">Thêm khách hàng</h2>
                                    <span class="mes-success" v-if="!!mesSucCus">{{ mesSucCus }}</span>
                                    <span class="mes-failed" v-if="!!mesFaiCus">{{ mesFaiCus }}</span>
                                </div>
                                <div class="row">
                                    <div class="spe-group col-lg-6">
                                        <label for="">Tên khách hàng <span class="required">*</span></label>
                                        <input
                                            type="text"
                                            required
                                            placeholder="Nhập tên khách hàng"
                                            v-model="customer.fullName"
                                        />
                                    </div>
                                    <div class="spe-group col-lg-6">
                                        <label for="">Số điện thoại<span class="required">*</span></label>
                                        <input
                                            type="text"
                                            required
                                            placeholder="Nhập Số điện thoại"
                                            v-model="customer.phone"
                                        />
                                        <span v-if="!!valid.phone" :class="{ 'text-danger': !!valid.phone }">{{
                                            valid.phone
                                        }}</span>
                                    </div>
                                    <div class="spe-group col-lg-6">
                                        <label for="">Email<span class="required">*</span></label>
                                        <input
                                            type="email"
                                            required
                                            placeholder="Nhập email của bạn"
                                            v-model="customer.email"
                                        />
                                        <span v-if="!!valid.email" :class="{ 'text-danger': !!valid.email }">{{
                                            valid.email
                                        }}</span>
                                    </div>
                                    <div class="spe-group col-lg-6">
                                        <label for="">Địa chỉ<span class="required">*</span></label>
                                        <input
                                            type="text"
                                            required
                                            placeholder="Nhập tên đường, hẻm, số nhà"
                                            v-model="customer.address"
                                        />
                                    </div>
                                </div>
                                <div class="row">
                                    <div class="group col-lg-4">
                                        <div class="d-flex mb-1">
                                            <label for="">Tỉnh/Thành phố <span class="required">*</span></label>
                                        </div> 
                                        <select v-model="VmodelAddress.city" name="" id="" class="form-select  w-100" @change="getDistricts(VmodelAddress.city)" required>
                                            <option value="" class="form-option">--- Chọn ---</option>
                                            <option v-for="item in city" :key="item.code" :value="item">{{ item.name }}</option>
                                        </select>
                                    </div>
                                    <div class="group col-lg-4">
                                        <div class="d-flex mb-1">
                                            <label for="">Quận/huyện <span class="required">*</span></label>
                                        </div>
                                        <select v-model="VmodelAddress.district" name="" id="" class="form-select  w-100" @change="getWards(VmodelAddress.district)" required>
                                            <option value="" class="form-option">--- Chọn ---</option>
                                            <option v-for="item in districts" :key="item.code" :value="item">{{ item.name }}</option>
                                        </select>
                                    </div>
                                    <div class="group col-lg-4">
                                        <div class="d-flex mb-1">
                                            <label for="">Phường/Xã <span class="required">*</span></label>
                                        </div>
                                        <select v-model="VmodelAddress.ward" name="" id="" class="form-select w-100" @change="saveAddress(VmodelAddress.ward)" required>
                                            <option value="" class="form-option">--- Chọn ---</option>
                                            <option v-for="item in wards" :key="item.code" :value="item">{{ item.name }}</option>
                                        </select>
                                    </div>
                                </div>
                                <div>
                                    <button class="btn btn-brand-submit mt-5">Thêm</button>
                                </div>
                            </form>
                        </div>
                        <!-- chọn khách hàng -->
                    <select v-model="customerId" class="form-select" required @change="getCustomerById(customerId)">
                        <option value="" class="form-option">--- Chọn khách hàng ---</option>
                        <option v-for="(customer, index) in customers" :key="index" class="form-option" :value="customer._id">
                            {{ customer.fullName }} <i class="fa-solid fa-pen"></i>
                        </option>
                    </select>
                    <!-- edit khách hàng -->
                    <div class="overlay" v-if="activeEditCustomer">
                        <form action="" class="customer-form" @submit.prevent.stop="editCustomer">
                            <div class="close-form-customer" @click="closeFormCustomerEdit">
                                <i class="fa-solid fa-xmark"></i>
                            </div>
                            <div class="form-head">
                                <h2 class="title">Xem và thay đổi thông tin</h2>
                                <span class="mes-success" v-if="!!mesSucCus">{{ mesSucCus }}</span>
                                <span class="mes-failed" v-if="!!mesFaiCus">{{ mesFaiCus }}</span>
                            </div>
                            <div class="row">
                                <div class="spe-group col-lg-4">
                                    <label for="">Tên khách hàng <span class="required">*</span></label>
                                    <input
                                        type="text"
                                        required
                                        placeholder="Nhập tên khách hàng"
                                        v-model="customerInfo.fullName"
                                    />
                                </div>
                                <div class="spe-group col-lg-4">
                                    <label for="">Số điện thoại<span class="required">*</span></label>
                                    <input
                                        type="text"
                                        required
                                        placeholder="Nhập Số điện thoại"
                                        v-model="customerInfo.phone"
                                    />
                                    <span v-if="!!valid.phone" :class="{ 'text-danger': !!valid.phone }">{{
                                        valid.phone
                                    }}</span>
                                </div>
                                <div class="spe-group col-lg-4">
                                    <label for="">Email<span class="required">*</span></label>
                                    <input
                                        type="email"
                                        required
                                        placeholder="Nhập email của bạn"
                                        v-model="customerInfo.email"
                                    />
                                    <span v-if="!!valid.email" :class="{ 'text-danger': !!valid.email }">{{
                                        valid.email
                                    }}</span>
                                </div>
                                <div class="spe-group col-lg-12" v-if="!activeEditAddress">
                                    <label for="">Địa chỉ<span class="required">*</span> 
                                    </label>
                                    <input
                                        type="text"
                                        required
                                        placeholder="Nhập tên đường, hẻm, số nhà"
                                        v-model="customerInfo.address"
                                        />
                                </div>
                                <div class="row"><span class="col-lg-3 ms-2 btn btn-warning" @click="activeEditAddress=!activeEditAddress">{{activeEditAddress ? 'Giữ địa chỉ cũ' : 'Tạo địa chỉ mới'}}</span></div>
                                <div class="spe-group col-lg-12" v-if="activeEditAddress">
                                    <label for="">Địa chỉ<span class="required">*</span></label>
                                    <input
                                        type="text"
                                        required
                                        placeholder="Nhập tên đường, hẻm, số nhà"
                                        v-model="customerInfo.address2"
                                    />
                                </div>
                            </div>
                            <div class="row" v-if="activeEditAddress">
                                <div class="group col-lg-4">
                                    <div class="d-flex mb-1">
                                        <label for="">Tỉnh/Thành phố <span class="required">*</span></label>
                                    </div> 
                                    <select v-model="VmodelAddress.city" name="" id="" class="form-select  w-100" @change="getDistricts(VmodelAddress.city)" required>
                                        <option value="" class="form-option">--- Chọn ---</option>
                                        <option v-for="item in city" :key="item.code" :value="item">{{ item.name }}</option>
                                    </select>
                                </div>
                                <div class="group col-lg-4">
                                    <div class="d-flex mb-1">
                                        <label for="">Quận/huyện <span class="required">*</span></label>
                                    </div>
                                    <select v-model="VmodelAddress.district" name="" id="" class="form-select  w-100" @change="getWards(VmodelAddress.district)" required>
                                        <option value="" class="form-option">--- Chọn ---</option>
                                        <option v-for="item in districts" :key="item.code" :value="item">{{ item.name }}</option>
                                    </select>
                                </div>
                                <div class="group col-lg-4">
                                    <div class="d-flex mb-1">
                                        <label for="">Phường/Xã <span class="required">*</span></label>
                                    </div>
                                    <select v-model="VmodelAddress.ward" name="" id="" class="form-select w-100" @change="saveAddress(VmodelAddress.ward)" required>
                                        <option value="" class="form-option">--- Chọn ---</option>
                                        <option v-for="item in wards" :key="item.code" :value="item">{{ item.name }}</option>
                                    </select>
                                </div>
                            </div>
                            <div>
                                <button class="btn btn-brand-submit mt-5">Sửa</button>
                            </div>
                        </form>
                    </div>
                </div>
                <div class="group col-lg-8">
                    <div class="w-100">
                       <div class="d-flex ms-2"> <label for="">Ghi chú</label></div>
                        <textarea class="note" v-model="note" placeholder="Ghi chú cho đơn hàng"></textarea>
                    </div>
                </div>
                <!-- danh sach san pham -->
                <div class="croll">
                    <span class="d-block mt-3">Danh sách sản phẩm</span>
                    <div class="row" v-for="(orderProduct, index) in ordersProducts" :key="index">
                        <div class="group col-lg-3">
                            <div class="d-flex mb-1">
                                <label for="" class="mt-0">Loại hàng hóa <span class="required">*</span></label>
                            </div>
                            <select v-model="orderProduct.typeProduct" id="" class="form-select" @change="handelGetProduct(orderProduct.typeProduct)">
                                <option value="" class="form-option">--- Chọn loại hàng ---</option>
                                <option value="product">Sản phẩm</option>
                                <option value="accessory">Phụ kiện</option>
                            </select>
                        </div>
                        <div class="group col-lg-3" v-if="orderProduct.typeProduct==='product'">
                            <div class="d-flex mb-1">
                                <label for="" class="mt-0">Danh sách hàng <span class="required">*</span></label>
                            </div>
                            <select v-model="orderProduct.productId" class="form-select" @change="getproductById(products,orderProduct.productId, index)" required>
                                <option value="" class="form-option">--- Chọn ---</option>
                                <option  v-for="product in products" :value="product._id" :key="product._id">{{ product.name }}</option>
                            </select>
                        </div>
                        <div class="group col-lg-3" v-if="orderProduct.typeProduct==='accessory'">
                            <div class="d-flex mb-1">
                                <label for="" class="mt-0">Danh sách hàng <span class="required">*</span></label>
                            </div>
                            <select v-model="orderProduct.productId" class="form-select" @change="getproductById(accessorys,orderProduct.productId, index)" required>
                                <option value="" class="form-option">--- Chọn ---</option>
                                <option  v-for="product in accessorys" :value="product._id" :key="product._id">{{ product.name }}</option>
                            </select>
                        </div>
                        <div class="spe-group col-lg-3" v-if="!!orderProduct.typeProduct">
                                <label  for="" class="mt-0 mb-0">Giá bán</label>
                                <span class="price">Giá: {{ orderProduct.priceSale }}</span>
                        </div>
                        <div class="spe-group col-lg-3" v-if="!!orderProduct.typeProduct">
                                <label  for="" class="mt-0 mb-0">Số lượng<span class="text-danger ms-2">*</span></label>
                                <input v-model="orderProduct.quantity" @keyup="(event)=>checkQuantity(event, orderProduct)"  required type="number" placeholder="Nhập số lượng hàng" />
                        </div>
                        
                    </div>
                    
                </div>
                <div class="row">
                    <div class="col-3 d-flex justify-content-center align-items-center mt-1">
                        <div class="group me-3">
                            <span class="btn btn-outline-success" @click="pushRow">Thêm dòng</span>
                        </div>
                        <div class="group">
                            <span class="btn btn-outline-danger" @click="popRow">Bớt dòng</span>
                        </div>
                    </div>
                    <div class="spe-group col-3">
                        <label  for="" class="mt-0">Tổng tiền sản phẩm</label>
                        <input type="text" v-model="totalCostOfProducts" disabled>
                    </div>
                    <div class="spe-group col-3">
                        <label  for="" class="mt-0">Phí vận chuyển</label>
                        <input type="text" v-model="transportFee" disabled>
                    </div>
                    <div class="spe-group col-3">
                        <label  for="" class="mt-0">Tổng cộng</label>
                        <input type="text" v-model="totalAmount" disabled>
                    </div>
                </div>
                <!-- hình thức thanh toán -->
                <div class="row mt-4">
                    <div class="col-lg-4">
                        <label for="payment" :class="{'payment-active': isClassCod, 'payment-error':choosePayent}" class="payment" @click=" paymentMeThod('COD')">
                                <img src="https://s3-ap-southeast-1.amazonaws.com/pharmacity-ecm-asm-dev/payment-method/cash.webp" alt="">
                                <div class="payment-content">
                                    <span>Tiền mặt</span>
                                    <span>Thanh toán khi nhận hàng</span>
                                </div>
                                <div class="payment-checkbox">
                                    <span></span>
                                </div>
                        </label>
                    </div>
                    <div class="col-lg-4">
                        <label for="payment" :class="{'payment-active': isClassOnlVNPAY, 'payment-error':choosePayent}" class="payment"  @click=" paymentMeThod('VNPAY')">
                                <img src="https://vnpay.vn/s1/statics.vnpay.vn/2023/9/06ncktiwd6dc1694418196384.png" alt="">
                                <div class="payment-content">
                                    <span>VNPay</span>
                                    <span>Thanh toán qua ví VNPay</span>
                                </div>
                                <div class="payment-checkbox">
                                    <span></span>
                                </div>
                        </label>
                    </div>
                    <div class="col-lg-4">
                        <label for="payment" :class="{'payment-active': isClassOnlMOMO, 'payment-error':choosePayent}" class="payment"  @click=" paymentMeThod('MOMO')">
                                <img src="https://upload.wikimedia.org/wikipedia/vi/f/fe/MoMo_Logo.png" alt="">
                                <div class="payment-content">
                                    <span>MOMO</span>
                                    <span>Quét mã QR momo</span>
                                </div>
                                <div class="payment-checkbox">
                                    <span></span>
                                </div>
                        </label>
                    </div>
                </div>
            </div>
            <button class="btn btn-success mt-3">Tạo đơn hàng</button>
        </form>
    </div>
</template>

<script>
import axios from 'axios';
import customerService from '@/service/customer.service'
import productService from '@/service/product.service';
import accessoryService from '@/service/accessory.service';
import orderService from '@/service/order.service'
export default {
    data(){
        return{
            activeCustomer:false,
            VmodelAddress:{city:'',district:'',ward:''},
            city:[],
            districts:[],
            wards:[],
            address:{},
            customer:{},
            valid:{},
            mesFaiCus:'',
            mesSucCus:'',
            customers:[],
            products:[],
            accessorys:[],
            product:{},
            ordersProducts:[{
                typeProduct:'',
                productId:'',
                priceSale:0
            }],
            customerId:'',
            isSubmit:true,
            paymentMethod:'',
            isClassCod:false,
            isClassOnlVNPAY:false,
            isClassOnlMOMO:false,
            choosePayent:false,
            paymentSuccess:null,
            note:'',
            transportFee:0,
            totalCostOfProducts:0,
            totalAmount:0,
            VAT:0,
            customerInfo:{},
            activeEditCustomer:false,
            activeEditAddress:false
        }
    },
    watch:{
        activeCustomer(){
            if(this.activeCustomer){
                this.getCity()
            }
        }
    },
    methods:{
        closeSuccess(){
            this.$router.push('/admin/order-create')
            this.paymentSuccess=null
        },
        totalProduct(){
            this.totalAmount=0
            this.transportFee=0
            this.totalCostOfProducts=0
            this.ordersProducts.forEach(product =>{
                if(product.priceSale && product.quantity)
                this.totalCostOfProducts+=product.priceSale*product.quantity
            })
            this.transportFee= Math.ceil(this.totalCostOfProducts* 0.01)
            this.totalAmount=this.transportFee+this.totalCostOfProducts+this.VAT
        },
        isPaymentSuccess() {
            const params = new URLSearchParams(window.location.search);
            if(params.get('success') === 'true'){
                this.paymentSuccess= true
            }
            else if(params.get('success') === 'false'){
                this.paymentSuccess= false
            }
        },
        // async getCity(){
        //     const res = await axios.get('https://provinces.open-api.vn/api/')
        //     this.city=[...res.data]
        // },
        async getDistricts(info){
            const code=info.code
            const res = await axios.get(`https://provinces.open-api.vn/api/p/${code}/?depth=2`)
            this.districts=[...res.data.districts],
            this.address.city=info.name
        },
        async getWards(info){
            const code=info.code
            this.address.district=info.name
            const res = await axios.get(`https://provinces.open-api.vn/api/d/${code}/?depth=2`)
            this.wards=[...res.data.wards]
        },
        saveAddress(info){
            this.address.ward=info.name
        },
        closeFormCustomer(){
            this.activeCustomer=false
            this.city=[]
            this.districts=[]
            this.wards=[],
           this.VmodelAddress={city:'',district:'',ward:''}
        },
        closeFormCustomerEdit(){
            this.activeEditCustomer=false
            this.activeEditAddress=false
            this.city=[]
            this.districts=[]
            this.wards=[],
           this.VmodelAddress={city:'',district:'',ward:''}
        },
        async addCustomer(){
            try {
                const isValid = this.validateForm(this.customer)
                const data={
                    ...this.customer
                }
                data.address=this.customer.address+', '+this.address.ward+', '+this.address.district+', '+this.address.city
                if(isValid){
                    const response = await customerService.create(data)
                    if(response.data.status){
                        this.mesSucCus=response.data.mes
                        this.mesFaiCus=''
                        this.VmodelAddress={city:'',district:'',ward:''}
                        this.customer={}
                        this.getAllCustomers()
                    }
                    else{
                        this.mesFaiCus=response.data.mes
                        this.mesSucCus=''
                    }
                }
            } catch (error) {
                console.log(error);
            }
            
        },

        async editCustomer(){
            try {
                const isValid = this.validateForm(this.customerInfo)
                const data={
                    ...this.customerInfo
                }
                data.address=this.activeEditAddress ? 
                    this.customerInfo.address2+', '+this.address.ward+', '+this.address.district+', '+this.address.city :
                    this.customerInfo.address
                if(isValid){
                    const response = await customerService.update(this.customerInfo._id,data)
                    if(response.data.status){
                        await this.getCustomerById(this.customerInfo._id)
                        this.mesSucCus=response.data.mes
                        this.mesFaiCus=''
                        this.VmodelAddress={city:'',district:'',ward:''}
                        this.activeEditAddress=false
                    }
                    else{
                        this.mesFaiCus=response.data.mes
                        this.mesSucCus=''
                    }
                }
            } catch (error) {
                console.log(error);
            }
            
        },
        async getCustomerById(id){
            try {
                const response = await customerService.getById(id)
                this.customerInfo = response.data
                this.activeEditCustomer=true
                this.mesSucCus=''
                this.mesFaiCus=''
            } catch (error) {
                console.log(error);
            }
        },
        validateForm(infoCustomer) {
            const phoneRegex = /^[0-9]+$/;
            const emailRegex = /^[^\s@]+@[^\s@]+\.[^\s@]+$/;
            if (!phoneRegex.test(infoCustomer.phone) || infoCustomer.phone.length < 10) {
                this.valid.phone = 'Số điện thoại không hợp lệ';
            } else {
                this.valid.phone = undefined;
            }

            if (!emailRegex.test(infoCustomer.email) || infoCustomer.email.length < 10) {
                this.valid.email = 'Email không hợp lệ';
            } else {
                this.valid.email = undefined;
            }

            let isValid = false;
            const arrayValid= Object.values(this.valid)
            for(let i=0;i<arrayValid.length;i++){
                if(arrayValid[i]===undefined){
                    isValid = true;
                } else {
                    isValid = false;
                    break
                }
            }
            return isValid;
        },
        async getAllCustomers(){
            try {
                const response = await customerService.getAll()
                this.customers=[...response.data]
            } catch (error) {
                console.log(error);
            }
        },
        async getAllProducts(){
            try {
                const response = await productService.getProducts()
                response.data.filter(product =>product.inputQuantity>0)
                this.products=response.data.filter(product =>product.inputQuantity>0)
            } catch (error) {
                console.log(error);
            }
        },
        async getAllAccessorys(){
            try {
                const response = await accessoryService.getAll()
                this.accessorys=response.data.filter(product =>product.inputQuantity>0)
            } catch (error) {
                console.log(error);
            }
        },
        handelGetProduct(typeProduct){
            if(typeProduct =='product'){
                this.getAllProducts()
            }
            else if(typeProduct=='accessory'){
                this.getAllAccessorys()
            }
            else if(typeProduct===''){
                this.products=[]
            }
        },
        getproductById(products,id,index){
            products.map(product => {
                if(product._id === id){
                    this.ordersProducts[index].priceSale=product.priceSale
                    this.ordersProducts[index].inputQuantity=product.inputQuantity
                    this.ordersProducts[index].priceImport=product.priceImport
                    this.ordersProducts[index].nameProduct=product.name
                    this.ordersProducts[index].warrantyTime=product.warrantyTime ? product.warrantyTime :null
                }
                else{
                    return;
                }
            })
        },
        pushRow(){
            this.ordersProducts.push({
                typeProduct:'',
                productId:'',
                priceSale:0
            })
            this.totalProduct()
        },
        popRow(){
            if(this.ordersProducts.length <=1){
                alert('Dòng cuối')
            }
            else{
                this.ordersProducts.pop()
                this.totalProduct()

            }
        },
        async addOrder(){
            try {
                const user = JSON.parse(sessionStorage.getItem('user'))
                if(this.paymentMethod ==''){
                    this.isSubmit=false
                    this.choosePayent=true
                }
                else{
                    this.isSubmit=true
                }
                
                const customer = await customerService.getById(this.customerId)

                const data={
                    createBy:user ? user.user._id : null,
                    customerId:this.customerId,
                    address:customer.data.address,
                    phone:customer.data.phone,
                    email:customer.data.email || '',
                    nameCustomer:customer.data.fullName,
                    products:[
                        ...this.ordersProducts
                    ],
                    totalCostOfProducts:this.totalCostOfProducts,
                    paymentMethod:this.paymentMethod,
                    transportFee: this.transportFee,
                    totalAmount:this.totalAmount,
                    note:this.note,
                    pricePayed:0
                }

                if(this.isSubmit){
                    if(this.paymentMethod =='VNPAY'){
                        const response = await orderService.create(data)
                        const resutl = response.data.data
                        if(response.data.status){
                            const payment = await orderService.paymentVNPAY('admin/order-create',{totalAmount: resutl.totalAmount, orderId:resutl._id})
                            window.location.href=payment.data
                        }
                    }
                    else if(this.paymentMethod =='MOMO'){
                        const response = await orderService.create(data)
                        const resutl = response.data.data
                        if(response.data.status){
                            const payment = await orderService.paymentMOMO('admin/order-create',{totalAmount: resutl.totalAmount, orderId:resutl._id})
                            window.location.href=payment.data
                        }
                    }
                    else{
                        const response = await orderService.create(data)
                        if(response.data.status){
                            this.products=[]
                            this.accessorys=[]
                            this.product={}
                            this.ordersProducts=[{
                                typeProduct:'',
                                idProduct:'',
                                quantity:0,
                                priceSale:0,
                            }],
                            this.customerId='',
                            this.isSubmit=true,
                            this.isClassCod=false
                            this.isClassOnl=false
                            this.totalAmount=0
                            this.totalCostOfProducts=0
                            this.transportFee=0
                            this.note='',
                            alert(response.data.mes)
                            
                        }
                        else{
                            alert(response.data.mes)
                        }
                    }
                }
                else{
                    alert('Kiểm tra lại dữ liệu đã nhập')
                }
            } catch (error) {
                console.log(error);
            }
            
        },
        checkQuantity(e,product){
            if(e.target.value <=0 || e.target.value > product.inputQuantity){
                e.target.style.borderColor='red'
                this.isSubmit=false
                this.totalAmount=0
                this.totalCostOfProducts=0
                this.transportFee=0
            }
            else{
                this.totalProduct()
                e.target.style.borderColor='#0E8388'
                this.isSubmit=true
            }
        },
        async paymentMeThod( type){
            this.choosePayent=false
            if(type=='COD'){
                this.isClassCod=!this.isClassCod
                if(this.isClassCod){
                    this.paymentMethod='COD'
                    this.isClassOnlVNPAY=false
                    this.isClassOnlMOMO=false
                }
                else{
                    this.paymentMethod=''
                }
            }
            else if(type == 'VNPAY'){
                this.isClassOnlVNPAY=!this.isClassOnlVNPAY
                if(this.isClassOnlVNPAY){
                    this.paymentMethod='VNPAY'
                    this.isClassCod=false
                    this.isClassOnlMOMO=false
                }
                else{
                    this.paymentMethod=''
                }
            }
            else if(type == 'MOMO'){
                this.isClassOnlMOMO=!this.isClassOnlMOMO
                if(this.isClassOnlMOMO){
                    this.paymentMethod='MOMO'
                    this.isClassCod=false
                    this.isClassOnlVNPAY=false

                }
                else{
                    this.paymentMethod=''
                }
            }
        }
    },
    mounted(){
        // this.getCity()
        this.getAllCustomers()
        this.isPaymentSuccess()
    }
};
</script>

<style scoped>
.admin-form-order {
    width: 100%;
    border: 1px solid #ccc;
    border-radius: 5px;
    padding:0 10px 10px 10px;
    min-height: 500px;
}
.spe-group label{
    margin-top: 15px;
}
.spe-group input{
    height: 45px;
    border-color: #0E8388;
}
.spe-group .price{
    display: flex;
    height: 45px;
    align-items: center;
    width: 100%;
    border: 1px solid #0E8388;
    border-radius: 5px;
    padding-left: 10px;
    background: #ccc;
}
.croll{
    height: 220px;
    overflow-y: scroll;
    scroll-behavior: smooth;
    border: 1px solid #ccc;
    margin: 10px 0px;
}

.customer-form{
    position: relative;
    top: 50%;
    left: 50%;
    transform: translate(-50%,-50%);
    width: 650px;
    min-height: 0px;
    background: #fff;
    padding: 10px 20px;
}

.close-form-customer{
    display: flex;
    justify-content: flex-end;
    align-items: center;
}
.close-form-customer i{
    color: red;
    font-size: 30px;
    cursor: pointer;
}
.payment{
    width: 100%;
    height: 80px;
    border: 1px solid #ccc;
    border-radius: 8px;
    display: flex;
    justify-content: space-around;
    align-items: center;
    cursor: pointer;
}
.payment-active{
    background: #f2f6fe;
    border-color: #0072bc;
}
.payment img{
    width: 40px;
    height: 40px;
}
.payment-content{
    display: flex;
    flex-direction: column;
    align-items: flex-start;
    width: 70%;
}
.payment-content span:nth-child(1){
    font-weight: 600;
    font-size: 18px;
}
.payment-checkbox span{
    width: 25px;
    height: 25px;
    border: 1px solid #ccc;
    display: block;
    border-radius: 50%;
    position: relative;
    background: #ccc;

}
.payment-checkbox span::before{
    content: '';
    position: absolute;
    width: 10px;
    height: 10px;
    border-radius: 50%;
    top: 50%;
    left: 50%;
    transform: translate(-50%,-50%);
    background: #fff;
}
.payment-active .payment-checkbox span{
    background: #0072bc;
}
.payment-error{
    border-color: red;
}
.modal-success{
    position: absolute;
    top: 50%;
    left: 55%;
    transform: translate(-50%,-50%);
    width: 400px;
    height: 200px;
    background: #fff;
    display: flex;
    align-items: center;
    flex-direction: column;
    justify-content: center;
}
.success-icon{
    height: 40%;
    width: 100%;
    display: flex;
    justify-content: center;
    align-items: center;
    margin-bottom: 10px;
}
.success-icon span{
    display: flex;
    justify-content: center;
    align-items: center;
    width: 90px;
    height: 90px;
    border: 1px solid;
    border-radius: 50%;
}
.success-icon span i{
    font-size: 50px;
}
.note{
    background-color: #fff;
    margin: 0;
    width: calc(100% - 20px);
    outline: none;
    padding: 10px;
    border-radius: 5px;
    height: 100px;
}
</style>
