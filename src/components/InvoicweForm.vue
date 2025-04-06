<template>
    <!-- mt upper padding -->
     <!-- mb lowwer padding  -->
    <div class="container mt-5" >
        <h2 class="text-center">Cypher Invoice Creator</h2>
        <div class="row d-flex justify-content-center align-items-center mb-4">
            <label for="inv-num" class="col-4">Invoice Number</label>
            <div class="col-8 inv-num">
                <input class="form-control" type="text" name="inv-num" id="inv-num" placeholder="Enter invoice number" v-model="form.invoiceNumber">
            </div>
        </div>

        <!-- new -->
        <h3>Client Information</h3>
        <div class="row mb-2">
            <div class="col">
                <input type="text" class="form-control" placeholder="Name" v-model="form.client.name">
            </div>
            <div class="col">
                <input type="text" class="form-control" placeholder="Phone Number" v-model="form.client.phone">
            </div>
        </div>
        <div class="row mb-2">
            <div class="col-12 mb-2">
                <input type="text" class="form-control" placeholder="Address" v-model="form.client.address">
            </div>
            <div class="col">
                <input type="text" class="form-control" placeholder="Email Address" v-model="form.client.email">
            </div>
        </div>

        <h3>Invoice Information</h3>
        <div class="row mt-2 d-flex justify-content-center align-items-center" v-for="(product, index) in form.products" :key="index">
            <div class="col-12 mb-2">
                <input type="text" class="form-control" placeholder="Product Name" v-model="product.name">
            </div>
            <div class="col-6 mb-2">
                <input type="text" class="form-control" placeholder="Quantity" v-model="product.quantity">
            </div>
            <div class="col-6 mb-2">
                <input type="text" class="form-control" placeholder="Rate" v-model="product.rate">      
            </div>
            <div class="col-6 mb-2">
                <input type="text" class="form-control" placeholder="Amount" :value="amount(index)">
            </div>
            <div class="col-5 text-danger mb-2 justify-content-end align-items-center d-flex ">
                <i @click="removeProduct(index)" class="fa-solid fa-square-minus fs-1"></i>
            </div>
            <div class="col-1 text-success mb-2 justify-content-end align-items-center d-flex">
                <!-- <button class="btn btn-success ">+</button> -->
                <i @click="addProduct" class="fa-solid fa-square-plus fs-1"></i>
            </div>
            <hr class="mt-4 mb-4 border border-2 border-dark">
        </div>

        
        <div class="payments">
            <h3>Payments</h3>
            <div class="row mb-2  d-flex justify-content-center align-items-center">
                <div class="col-5 mb-4 apply-costs d-flex flex-column justify-content-center">
                    <label for="discount" >Apply Discount</label>
                    <input type="text" id="discount" class=" form-control mb-2" :placeholder="form.payment.discount === 0 || form.payment.discount === ''  ? 'Discount': ''" v-model="form.payment.discount">
                    <label for="delivery">Delivery Cost</label>
                    <input type="text" id="delivery" class=" form-control mb-2" placeholder="Delivery Cost" v-model="form.payment.deliveryCost">
                    <label for="paid">Amount Paid</label>
                    <input type="text" id="paid" class="form-control" placeholder="Amount Paid" v-model="form.payment.amountPaid">
                </div>

                <div class="row col-7 total-calculation">
                    <div class="col-12 d-flex align-items-center">
                        <p class="col-6 text-end text-success">Subtotal:</p>
                        <p class="col-6 text-end text-success">{{ subtotal }}</p>
                    </div>
                    <div class="col-12 d-flex align-items-center">
                        <p class="col-6 text-end text-danger">Discount:</p>
                        <p class="col-6 text-end text-danger">{{ -discount }}</p>
                    </div>
                    <div class="col-12 d-flex align-items-center">
                        <p class="col-6 text-end text-success">Delivery:</p>
                        <p class="col-6 text-end text-success">{{ deliveryCost }}</p>
                    </div>
                    <div class="col-12 d-flex align-items-center">
                        <p class="col-6 text-end text-success">Payable:</p>
                        <p class="col-6 text-end text-success">{{ payable }}</p>
                    </div>
                    <div class="col-12 d-flex align-items-center">
                        <p class="col-6 text-end text-success">Due:</p>
                        <p class="col-6 text-end text-success">{{ due }}</p>
                    </div>
                </div>
                               
                <payment-select  @payment-selected="handlePaymentSelect"/>
                
                
            </div>
        </div>
        <div class="submit text-center mb-5">
            <!-- <button class="btn btn-success" @click="handleSubmit()">Submit</button> -->
            <!-- <button class="btn btn-primary" @click="downloadPDF">Download PDF</button> -->
        </div>
        



        <div>
    <!-- The content of the invoice to be rendered in the PDF -->
    <div class="container py-4">
    <!-- PDF content -->
    <div id="invoice-content" class="border rounded p-4 bg-white">
      <!-- Header -->
      <div class="d-flex justify-content-between align-items-center mb-4">
        <div>
            <h3 class="mb-0">Cypher Invoice</h3>
            <small>Invoice: {{ form.invoiceNumber || 'N/A' }}</small><br>
            <small>Date: {{ new Date().toLocaleDateString() }}</small>
        </div>
        <div>
            <img src="../assets/YPHER.png" alt="Logo" class="img-fluid" style="width: 150px; height: auto;">
        </div>
        </div>


      <!-- Client Info -->
      <div class="mb-4">
        <h5>Client Information</h5>
        <p class="mb-0"><strong>Name:</strong> {{ form.client.name }}</p>
        <p class="mb-0"><strong>Phone:</strong> {{ form.client.phone }}</p>
        <p class="mb-0"><strong>Address:</strong> {{ form.client.address }}</p>
        <p class="mb-0"><strong>Email:</strong> {{ form.client.email }}</p>
      </div>

      <!-- Products Table -->
      <div class="mb-4">
        <h5>Product Details</h5>
        <table class="table table-bordered table-striped">
          <thead class="table-primary">
            <tr>
              <th>#</th>
              <th>Product Name</th>
              <th class="text-end">Quantity</th>
              <th class="text-end">Rate</th>
              <th class="text-end">Amount</th>
            </tr>
          </thead>
          <tbody>
            <tr v-for="(product, index) in form.products" :key="index">
              <td>{{ index + 1 }}</td>
              <td>{{ product.name }}</td>
              <td class="text-end">{{ product.quantity }}</td>
              <td class="text-end">{{ product.rate }}</td>
              <td class="text-end">{{ (product.quantity * product.rate).toFixed(2) }}</td>
            </tr>
          </tbody>
        </table>
      </div>

      <!-- Payment Summary -->
      <div class="row justify-content-end">
        <div class="col-md-6">
          <table class="table table-sm">
            <tbody>
              <tr>
                <th>Subtotal</th>
                <td class="text-end">{{ subtotal }}</td>
              </tr>
              <tr>
                <th>Discount</th>
                <td class="text-end">-{{ discount }}</td>
              </tr>
              <tr>
                <th>Delivery Cost</th>
                <td class="text-end">{{ deliveryCost }}</td>
              </tr>
              <tr class="table-active">
                <th>Total Payable</th>
                <td class="text-end fw-bold">{{ payable }}</td>
              </tr>
              <tr>
                <th>Amount Paid</th>
                <td class="text-end">{{ form.payment.amountPaid }}</td>
              </tr>
              <tr class="table-warning">
                <th>Due</th>
                <td class="text-end text-danger">{{ due.toFixed(2) }}</td>
              </tr>
              <tr class="table-warning">
                <th>Payment Method</th>
                <td class="text-end text-danger">{{ form.payment.method }}</td>
              </tr>
            </tbody>
          </table>
        </div>
      </div>
    </div>

    <!-- PDF Button -->
    <div class="text-center mt-4">
      <button class="btn btn-primary" @click="downloadPDF">Download PDF</button>
    </div>
  </div>
  </div>
    </div>  
    
    
    
</template>

<script>
// logic will come later
import { computed } from 'vue';
import PaymentSelect from './PaymentSelect.vue';
import html2pdf from 'html2pdf.js';
    export default {
        components: {
            PaymentSelect
        },
        props: {
            invoiceNumber: {
                type: String,
                required: true
            },
            paymentSelect: {
                type: String,
                required: true
            },
        },
        data() {
            return {
               form: {
                    invoiceNumber: '',
                    displayDiscount: '',
                    displayDeliveryCost: '',
                    displayAmountPaid: '',
                    displaySubtotal : '', 
                    
                    
                    client: {
                        name: '',
                        phone: '',
                        address: '',
                        email: ''
                    },
                    products: [
                        {   
                            name: '',
                            quantity: '',
                            rate: '',
                            amount: ''
                        },
                    ], 
                    
                    
                    payment: {
                        method: '',
                        amountPaid: '',
                        discount: '',
                        deliveryCost: '',
                        subtotal: '',
                        total: '',
                        payable: '',
                        due: '',
                    },
                      
               },
               
            }
        },
        methods: {
            handlePaymentSelect(payment) {
                this.form.payment.method = payment;
                console.log('Selected payment method:', payment);
            },
            handleSubmit() {
                console.log(this.form);
                // Handle form submission logic here
            },
            addProduct() {
                const lastProduct = this.form.products[this.form.products.length - 1];

                if (
                    lastProduct.name === '' ||
                    lastProduct.quantity === '' ||
                    lastProduct.rate === ''
                ) {
                    alert('Please fill in all product fields before adding a new product.');
                    return;
                }
                this.form.products.push({ name: '', quantity: '', rate: '', amount: '' })
                console.log(this.form.products);
            },
            removeProduct(index) {
                if (this.form.products.length > 1) {
                    this.form.products.splice(index, 1);
                } else {
                    alert('At least one product is required.');
                }
            },
            amount(index) {
                const product = this.form.products[index];
                const quantity = parseFloat(product.quantity) || 0;
                const rate = parseFloat(product.rate) || 0;
                return quantity * rate;
            },
            downloadPDF() {
      const element = document.getElementById('invoice-content');
      
      const options = {
        margin:       0.5,
        filename:     `Invoice_${this.form.invoiceNumber || 'No_Number'}.pdf`,
        image:        { type: 'jpeg', quality: 0.98 },
        html2canvas:  { scale: 2 },
        jsPDF:        { unit: 'in', format: 'a4', orientation: 'portrait' }
      };

      html2pdf().set(options).from(element).save();
    },
        },
        computed: {
            subtotal() {
                // return this.form.product.quantity * this.form.product.rate;
                return this.form.products.reduce((acc, product) => {
                    return acc + (product.quantity * product.rate);
                }, 0);
            },
            
            discount() {
                return this.form.payment.discount;
            },
            deliveryCost() {
                return this.form.payment.deliveryCost;
            },
            payable() {
                if(this.discount === 0 || this.discount === '') {
                    this.form.payment.payable =  +this.subtotal + +this.deliveryCost;
                } else if (this.discount > this.subtotal) {
                    this.form.payment.payable =  0;
                } 
                else if(this.discount > 0) {
                    this.form.payment.payable =  +this.subtotal - +this.discount + +this.deliveryCost;
                }
                else {
                    this.form.payment.payable =  +this.subtotal - +this.discount + +this.deliveryCost;
                }
                return this.form.payment.payable;
            },
            due() {
                return this.form.payment.payable - this.form.payment.amountPaid;
            },

        },
        watch: {
            'form.products': {
                handler(newProducts) {
                newProducts.forEach(product => {
                    const quantity = parseFloat(product.quantity) || 0;
                    const rate = parseFloat(product.rate) || 0;
                    product.amount = quantity * rate;
                });
                },
                deep: true
            }
            }

        
    }
</script>
  