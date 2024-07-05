<template>
  <div class="invoice-generator">
    <!-- Input Section -->
    <div class="input-section">
      <!-- Invoice Details -->
      <section class="input-group">
        <h3>Invoice Details</h3>
        <input
          v-model="invoiceNumberInput"
          placeholder="Invoice Number"
          class="styled-input"
        />
        <div class="date-input">
          <input
            v-if="!invoiceDateFocused"
            v-model="invoiceDatePlaceholder"
            placeholder="Invoice Date"
            @focus="focusInvoiceDate"
            class="styled-input"
          />
          <input
            v-else
            v-model="invoiceDateInput"
            type="date"
            @blur="blurInvoiceDate"
            class="styled-input"
          />
        </div>
        <div class="date-input">
          <input
            v-if="!dueDateFocused"
            v-model="dueDatePlaceholder"
            placeholder="Due Date"
            @focus="focusDueDate"
            class="styled-input"
          />
          <input
            v-else
            v-model="dueDateInput"
            type="date"
            @blur="blurDueDate"
            class="styled-input"
          />
        </div>
      </section>

      <!-- Buyer Information -->
      <section class="input-group">
        <h3>Buyer Information</h3>
        <input
          v-model="clientNameInput"
          placeholder="Client Name"
          class="styled-input"
        />
        <input
          v-model="clientAddressInput"
          placeholder="Client Address"
          class="styled-input"
        />
        <input
          v-model="clientEmailInput"
          placeholder="Client Email"
          class="styled-input"
        />
        <input
          v-model="clientNumberInput"
          placeholder="Client Number"
          class="styled-input"
        />
      </section>

      <!-- Product Information -->
      <section class="input-group">
      <h3>Product Information</h3>
      <div v-for="(inputRow, index) in inputRows" :key="index" class="product-row">
        <input v-model="inputRow.item" placeholder="Item/Service" class="styled-input full-width">
        <div class="price-quantity-row">
          <input v-model="inputRow.price" placeholder="Price" class="styled-input half-width">
          <input v-model="inputRow.quantity" placeholder="Quantity" class="styled-input half-width">
        </div>
        <button v-if="index > 0" @click="removeItem(index)" class="remove-btn">-</button>
      </div>
      <button @click="addItem" class="add-btn">+ Add Item</button>
    </section>

      <!-- VAT and Discount -->
      <section class="input-group">
        <input
          v-model="vatPercentage"
          type="number"
          :placeholder="vatPlaceholder"
          class="styled-input"
        />
        <input
          v-model="discountPercentage"
          type="number"
          :placeholder="discountPlaceholder"
          class="styled-input"
        />
      </section>

      <!-- Seller Information -->
      <section class="input-group">
        <h3>Seller Information</h3>
        <input
          v-model="companyNameInput"
          placeholder="Company Name"
          class="styled-input"
        />
        <input
          v-model="companyAddressInput"
          placeholder="Company Address"
          class="styled-input"
        />
        <input
          v-model="companyEmailInput"
          placeholder="Company Email"
          class="styled-input"
        />
        <input
          v-model="companyNumberInput"
          placeholder="Company Number"
          class="styled-input"
        />
      </section>

      <button @click="printInvoice" class="generate-btn">
        Print/Export to PDF
      </button>
    </div>

    <!-- Invoice Output Section -->
    <div class="preview-section" ref="invoicePreview">
      <div class="invoice-header">
        <div class="invoice-number">
        <h1>Invoice: {{ invoiceNumberInput }}</h1>
          <p>Invoice Date: {{ invoiceDateInput }}</p>
          <p>Due Date: {{ dueDateInput }}</p>
        </div>
        <div class="client-info">
          <h2>{{ clientNameInput || 'Client Name' }}</h2>
          <p>{{ clientAddressInput || 'Client Address' }}</p>
          <p>{{ clientEmailInput || 'Email' }}</p>
          <p>{{ clientNumberInput || 'Phone Number' }}</p>
        </div>
      </div>

      <div class="total-due">
        <h3>Total Due</h3>
        <h2>Sum of all charges</h2>
      </div>

      <div class="invoice-table">
        <table>
          <thead>
            <tr>
              <th>Description</th>
              <th>Cost ($)</th>
              <th>QTY</th>
              <th>Amount</th>
            </tr>
          </thead>
          <tbody>
            <tr v-for="(item, index) in items" :key="index">
              <td>{{ item.item }}</td>
              <td>{{ item.price }} $</td>
              <td>{{ item.quantity }}</td>
              <td>{{ item.total }} $</td>
            </tr>
          </tbody>
        </table>
      </div>

      <div class="invoice-summary">
        <div class="summary-row">
          <span>Subtotal:</span>
          <span>{{ totalSum.toFixed(2) }}$</span>
        </div>
        <div class="summary-row">
          <span>VAT ({{ vatPercentage }}%):</span>
          <span>{{ vatAmount }}$</span>
        </div>
        <div class="summary-row">
          <span>Discount ({{ discountPercentage }}%):</span>
          <span>{{ discountAmount }}$</span>
        </div>
        <div class="summary-row total">
          <span>Total Due:</span>
          <span>{{ totalDue }}$</span>
        </div>
      </div>

      <div class="invoice-footer">
      <div class="footer-content">
          <!-- Footer content here -->
          <div class="seller-info">
              <p>{{ companyNameInput || 'Company Name' }}</p>
              <p>{{ companyAddressInput || 'Address' }}</p>
              <p>{{ companyEmailInput || 'Email' }}</p>
              <p>{{ companyNumberInput || 'Phone Number' }}</p>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      inputRows: [{ item: '', price: '', quantity: '' }],
      items: [],
      invoiceNumberInput: '',
      invoiceDateInput: '',
      dueDateInput: '',
      invoiceDatePlaceholder: '',
      dueDatePlaceholder: '',
      invoiceDateFocused: false,
      dueDateFocused: false,
      clientNameInput: '',
      clientAddressInput: '',
      clientEmailInput: '',
      clientNumberInput: '',
      companyNameInput: '',
      companyAddressInput: '',
      companyEmailInput: '',
      companyNumberInput: '',
      vatPercentage: '',
      discountPercentage: '',
    };
  },
  computed: {
    vatPlaceholder() {
      return this.vatPercentage === '' ? 'VAT Percentage' : '';
    },
    discountPlaceholder() {
      return this.discountPercentage === '' ? 'Discount Percentage' : '';
    },
    showQuantityColumn() {
      return this.inputRows.some(
        (row) => row.quantity !== '' && parseFloat(row.quantity) >= 0
      );
    },
    showQuantityInTable() {
      return this.inputRows.some(
        (row) => row.quantity !== '' && parseFloat(row.quantity) > 0
      );
    },
    totalSum() {
      return this.items.reduce(
        (total, item) => total + parseFloat(item.total || 0),
        0
      );
    },
    vatAmount() {
      return (
        (this.totalSum * parseFloat(this.vatPercentage || 0)) /
        100
      ).toFixed(2);
    },
    discountAmount() {
      return (
        (this.totalSum * parseFloat(this.discountPercentage || 0)) /
        100
      ).toFixed(2);
    },
    totalDue() {
      return (
        this.totalSum +
        parseFloat(this.vatAmount) -
        parseFloat(this.discountAmount)
      ).toFixed(2);
    },
  },
  methods: {
    addItem() {
      this.inputRows.push({ item: '', price: '', quantity: '' });
    },
    removeItem(index) {
      if (index > 0) {
        this.inputRows.splice(index, 1);
      }
    },
    focusInvoiceDate() {
      this.invoiceDateFocused = true;
      this.$nextTick(() => {
        this.$refs.invoiceDateInput.focus();
      });
    },
    blurInvoiceDate() {
      if (!this.invoiceDateInput) {
        this.invoiceDateFocused = false;
      }
    },
    focusDueDate() {
      this.dueDateFocused = true;
      this.$nextTick(() => {
        this.$refs.dueDateInput.focus();
      });
    },
    blurDueDate() {
      if (!this.dueDateInput) {
        this.dueDateFocused = false;
      }
    },
    printInvoice() {
      const printSection = this.$refs.invoicePreview;
      const printWindow = window.open('', '', 'height=800,width=600');
      printWindow.document.write('<html><head><title>Invoice</title>');
      const styles = document.querySelectorAll('style, link[rel="stylesheet"]');
      styles.forEach((style) => {
        printWindow.document.write(style.outerHTML);
      });
      printWindow.document.write('</head><body>');
      printWindow.document.write(printSection.outerHTML);
      printWindow.document.write('</body></html>');
      printWindow.document.close();
      printWindow.print();
    },
  },
  watch: {
    inputRows: {
      handler(newValue) {
        this.items = newValue.map((row) => ({
          item: row.item,
          price: row.price,
          quantity: parseFloat(row.quantity || 0),
          total: row.price * (parseFloat(row.quantity || 0) || 1),
        }));
      },
      deep: true,
    },
  },
};
</script>

<style scoped>
/* General Styles */
body {
  font-family: Arial, sans-serif;
  margin: 0;
  padding: 0;
  background-color: #f0f0f0;
}

.invoice-generator {
  font-family: Arial, sans-serif;
  display: flex;
  height: 100vh;
}

/* Input Section Styles */
.input-section {
  flex: 1;
  padding: 20px;
  overflow-y: auto;
  background-color: #f8f8f8;
}

.input-group {
  margin-bottom: 20px;
}

.input-group h3 {
  margin-bottom: 10px;
  color: #333;
}

.styled-input {
  width: 100%;
  padding: 10px;
  margin-bottom: 10px;
  border: 1px solid #ccc;
  border-radius: 4px;
  font-size: 14px;
}

/* Date Input Styles */
.date-input {
  position: relative;
}

/* Product Information Styles */
.product-row {
  margin-bottom: 15px;
}

.price-quantity-row {
  display: flex;
  gap: 10px;
}

.full-width {
  width: 100%;
}

.half-width {
  width: calc(50% - 5px);
}

.add-btn, .remove-btn {
  background-color: #ff8c00;
  color: white;
  border: none;
  border-radius: 50%;
  width: 30px;
  height: 30px;
  font-size: 20px;
  cursor: pointer;
  display: flex;
  align-items: center;
  justify-content: center;
}

.remove-btn {
  background-color: #ff4500;
}

.add-btn {
  margin-top: 10px;
  width: auto;
  padding: 5px 15px;
  border-radius: 4px;
}

/* Generate Button */
.generate-btn {
  width: 100%;
  padding: 15px;
  background-color: #ff8c00;
  color: white;
  border: none;
  border-radius: 4px;
  font-size: 16px;
  cursor: pointer;
  margin-top: 20px;
}

/* Preview Section Styles */
.preview-section {
  font: arial;
  flex: 1;
  background-color: white;
  padding: 40px;
  overflow-y: auto;
  box-shadow: 0 0 10px rgba(0,0,0,0.1);
}

.preview-content {
  width: 210mm;
  min-height: 297mm;
  padding: 20mm;
  margin: 0 auto;
  background: white;
  box-shadow: 0 0 10px rgba(0,0,0,0.1);
  font-size: 12pt;
  line-height: 1.3;
  transform-origin: top left;
  transform: scale(0.7); /* Adjust this value to fit your screen */
}

.invoice-header {
  display: flex;
  justify-content: space-between;
  margin-bottom: 40px;
}

.invoice-number h1 {
  font-size: 28px;
  font-weight: bold;
  margin-bottom: 10px;
}

.invoice-number p, .client-info p {
  margin: 5px 0;
  color: #555;
  font-size: 14px;
}

.client-info {
  text-align: right;
}

.total-due {
  margin-bottom: 30px;
}

.total-due h3 {
  font-size: 16px;
  color: #555;
  margin-bottom: 5px;
}

.total-due h2 {
  font-size: 28px;
  font-weight: bold;
}

.invoice-table {
  margin-bottom: 30px;
}

.invoice-table table {
  width: 100%;
  border-collapse: collapse;
}

.invoice-table th, .invoice-table td {
  padding: 12px;
  text-align: left;
  border-bottom: 1px solid #e0e0e0;
}

.invoice-table th {
  background-color: #f8f8f8;
  font-weight: bold;
  text-transform: uppercase;
  font-size: 12px;
  color: #333;
}

.invoice-summary {
  margin-bottom: 30px;
}

.summary-row {
  display: flex;
  justify-content: space-between;
  padding: 10px 0;
  font-size: 14px;
}

.summary-row.total {
  font-weight: bold;
  font-size: 16px;
  margin-top: 10px;
}

.seller-info {
  margin-top: auto;
  padding-top: 20px;
  border-top: 1px solid #e0e0e0;
  color: #555;
  font-size: 14px;
}



/* Responsive Styles */
@media (max-width: 1024px) {
  .invoice-generator {
    flex-direction: column;
  }
  
  .preview-content {
    transform: scale(1);
    width: 100%;
    padding: 10mm;
  }
}

/* Print Styles */
@media print {
  .input-section {
    display: none;
  }
  .preview-section {
    padding: 0;
  }
  .preview-content {
    width: 210mm;
    height: 297mm;
    margin: 0;
    padding: 0;
    transform: none;
    box-shadow: none;
  }
}
</style>