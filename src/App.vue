<template>
  <div>
    <button v-if="!isOAuth" class="btn-auth btn btn-danger" @click="runOAuth()">
      Kích hoạt
    </button>
    <div v-if="isOAuth">
    <!-- <div> -->
      <div class="content">
        <div class="header px-1 py-2">
          <div class="content-header d-flex justify-content-between">
            <h5 class="p-1">Địa chỉ giao hàng</h5>
            <button type="button" class="btn btn-primary" @click="reConfig()">
              Đặt lại store
            </button>
          </div>
          <div class="form-row pt-2">
            <div class="col input-group-sm">
              <input
                type="text"
                class="form-control"
                placeholder="Họ và tên"
                v-model.trim="fullName"
              />
            </div>
            <div class="col input-group-sm">
              <input
                type="number"
                class="form-control"
                placeholder="Số điện thoại"
                v-model.trim="phoneNumber"
              />
            </div>
          </div>
          <div class="form pt-2 input-group-sm">
            <input
              type="text"
              class="form-control"
              placeholder="Địa chỉ"
              v-model.trim="address"
            />
          </div>
          <div class="form-row pt-2">
            <div class="col input-group-sm">
              <select type="text" class="form-control" v-model="branchId">
                <option value="">Chi nhánh</option>
                <option
                  v-for="(item, index) in listBranch"
                  :key="index"
                  :value="item.id"
                  >{{ item.branchName }}</option
                >
              </select>
            </div>
            <div class="col input-group-sm">
              <select
                type="text"
                class="form-control"
                v-model="city"
                @change="getDistrict()"
              >
                <option value="">Tỉnh/Thành</option>
                <option
                  v-for="(item, index) in listCity"
                  :key="index"
                  :value="item"
                  >{{ item.name }}</option
                >
              </select>
            </div>
          </div>
          <div class="form-row pt-2">
            <div class="col input-group-sm">
              <select
                type="text"
                class="form-control"
                v-model="district"
                @change="getWard()"
              >
                <option value="">Quận/Huyện</option>
                <option
                  v-for="(item, index) in listDistrict"
                  :key="index"
                  :value="item"
                  >{{ item.name }}</option
                >
              </select>
            </div>
            <div class="col input-group-sm">
              <select type="text" class="form-control" v-model="ward">
                <option value="">Phường/Xã</option>
                <option
                  v-for="(item, index) in listWard"
                  :key="index"
                  :value="item.name"
                  >{{ item.name }}</option
                >
              </select>
            </div>
          </div>
        </div>
        <!-- header -->
        <div class="body">
          <div class="add-product">
            <button
              class="btn btn-primary btn-show-modal ml-2 mr-5"
              type="button"
              @click="openModalAddCart"
            >
              <i class="fas fa-plus-circle"></i>
              <span> Thêm sản phẩm</span>
            </button>
            <div v-if="isShowModalProduct" class="modal-add-product">
              <button class="btn-close" @click="closeAddProduct()">
                &times;
              </button>
              <div class="search">
                <div class="input-group mb-2">
                  <div class="input-group-prepend">
                    <div class="input-group-text">
                      <i class="fas fa-search"></i>
                    </div>
                  </div>
                  <input
                    type="text"
                    class="form-control"
                    id="inlineFormInputGroup"
                    placeholder="Search"
                    v-model="search"
                  />
                </div>
              </div>
              <!-- input search -->
              <div class="show-product">
                <div
                  class="row"
                  v-for="(item, index) in filteredList"
                  :key="index"
                  @click="addProductForCart(item)"
                >
                  <div class="col-2">
                    <img :src="item.image || imageDefault" />
                  </div>
                  <div class="col-9">
                    <p>{{ item.product_name }}</p>
                    <div class="d-flex justify-content-between">
                      <span>{{ item.product_price }}đ</span>
                    </div>
                  </div>
                </div>
              </div>
              <!-- show product -->
            </div>
          </div>
          <!-- modal thêm sản phẩm vào giỏ hàng -->
          <div class="btn-add-note">
            <button class="btn btn-primary" type="button" @click="openNote()">
              <i class="fas fa-poll-h"></i>
              <span> Thêm ghi chú</span>
            </button>
          </div>
          <div v-if="isShowModalNote" class="modal-note">
            <div class="form-group">
              <textarea
                class="form-control"
                id="exampleFormControlTextarea1"
                rows="3"
                v-model="note"
              ></textarea>
            </div>
            <button
              type="button"
              class="btn btn-primary btn-sm"
              @click="closeNote()"
            >
              Thêm
            </button>
          </div>
          <!-- modal-note -->
          <table class="table mt-1">
            <tbody>
              <tr v-for="(item, index) in cart" :key="index">
                <td class="pl-1">
                  <img :src="item.other_info || imageDefault" />
                </td>
                <td>
                  <div>
                    <p>{{ item.product_name }}</p>
                  </div>
                </td>
                <td class="input-sort">
                  <div class="d-flex justify-content-start">
                    <fieldset disabled>
                      <div class="form-group">
                        <input
                          type="text"
                          class="form-control"
                          :value="item.product_quantity"
                        />
                      </div>
                    </fieldset>
                    <div>
                      <i
                        class="fas fa-chevron-up"
                        @click="handleAddCart(item.product_id)"
                      ></i>
                      <i
                        class="fas fa-chevron-down"
                        @click="handleSubCart(item.product_id)"
                      ></i>
                    </div>
                  </div>
                </td>
                <td class="py-2">
                  <p>{{ item.product_price * item.product_quantity }}đ</p>
                </td>
                <td>
                  <div class="py-2 pr-1">
                    <i
                      class="fas fa-trash-alt"
                      @click="handleDeleteItemCart(item.product_id)"
                    ></i>
                  </div>
                </td>
              </tr>
            </tbody>
          </table>
        </div>
        <!-- body -->
        <div class="bill-detail mx-1">
          <div class="row">
            <div class="col-6 bill-left">
              <p>Tổng Tiền</p>
            </div>
            <div class="col-6 bill-right">
              <p>
                <strong>{{ total }}đ</strong>
              </p>
            </div>
          </div>
        </div>
        <!-- bill -->
        <div class="footer d-flex flex-row-reverse mx-5">
          <button
            type="button"
            class="btn btn-primary btn-block btn-sm"
            @click="createBill()"
          >
            Tạo đơn hàng
          </button>
        </div>
        <!-- footer -->
      </div>
      <!-- content -->
      <div v-if="!checkModal">
        <div v-if="checkIdAndSecret" class="modal-get-info pt-3 px-2">
          <div class="form-group input-group-sm">
            <label>CLient Id:</label>
            <input
              type="text"
              class="form-control"
              placeholder="Client Id"
              v-model="clientId"
            />
          </div>
          <div class="form-group input-group-sm">
            <label>Client Secret:</label>
            <input
              type="text"
              class="form-control"
              placeholder="Client Secret"
              v-model="clientSecret"
            />
          </div>
          <button
            type="button"
            class="btn btn-primary btn-block btn-sm"
            @click="getAccessToken()"
          >
            Xác nhận
          </button>
        </div>
        <div
          v-if="checkStoreAndRetailer"
          class="modal-form-sync-product pt-3 px-2"
        >
          <div class="form-group input-group-sm">
            <label>Store Id:</label>
            <input
              type="text"
              class="form-control"
              placeholder="Store id"
              v-model="storeId"
            />
          </div>
          <div class="form-group input-group-sm">
            <label>Retailer Id:</label>
            <input
              type="text"
              class="form-control"
              placeholder="Retailer id"
              v-model="retailer"
            />
          </div>
          <button
            type="button"
            class="btn btn-primary btn-block btn-sm"
            @click="syncProduct()"
          >
            Xác nhận
          </button>
        </div>
      </div>
    </div>
  </div>
</template>
<script>
import axios from "axios";
import Swal from "sweetalert2";
import Restful from "./service/restful.js";
let host = "https://ext.botup.io/v1";
let url_string = location.href;
let url = new URL(url_string);
let access_token = url.searchParams.get("access_token");
const APIBase = "https://app.devchatbox.tk";
const Toast = Swal.mixin({
  toast: true,
  position: "top-end",
  showConfirmButton: false,
  timer: 2000,
  timerProgressBar: false,
  onOpen: (toast) => {
    toast.addEventListener("mouseenter", Swal.stopTimer);
    toast.addEventListener("mouseleave", Swal.resumeTimer);
  },
});
export default {
  data() {
    return {
      isOAuth: false,
      secret_key: "afc0e008b5d44efc9d689545bd1e2769",
      access_token: access_token,
      storeId: "5f27b3e89e3fcd007754ef2c",
      retailer: "tuonggodevuong",
      accessTokenKiotviet: "",
      clientId: "4b2ae4e1-f839-4d40-80fc-33dab03404fe",
      clientSecret: "02335391567814387AD05C073C2AA268E1C53D5B",
      imageDefault:
        "https://saveabandonedbabies.org/wp-content/uploads/2015/08/default.png",
      fullName: "",
      phoneNumber: "",
      address: "",
      city: "",
      district: "",
      ward: "",
      branchId: "",
      listBranch: [],
      listCountry: [],
      listCity: [],
      listDistrict: [],
      listWard: [],
      totalPrice: 0,
      promotion: "",
      note: "",
      cart: [],
      cartTotalPrice: 0,
      listProduct: [],
      search: "",
      isShowModalProduct: false,
      isShowModalNote: false,
      isConfig: false,
      checkIdAndSecret: true,
      checkStoreAndRetailer: false,
    };
  },
  created() {
    this.partnerAuthenticate();
    this.getCity();
    this.getBranch();
    this.getCart();
  },
  mounted() {},
  computed: {
    checkModal() {
      if (
        localStorage.getItem("store_id") &&
        localStorage.getItem("client_id") &&
        localStorage.getItem("client_secret")
      ) {
        this.isConfig = true;
      } else {
        this.isConfig = false;
      }
      return this.isConfig;
    },
    //tìm kiếm sp
    filteredList() {
      return this.listProduct.filter((product) => {
        return product.product_name
          .toLowerCase()
          .includes(this.search.toLowerCase());
      });
    },
    //tính tống giá trị giỏ hàng
    total() {
      let arr = this.cart;
      let total = [];
      let totalPrice = 0;
      if (arr.length === 0) {
        totalPrice = 0;
      } else {
        arr.forEach((item) => {
          let totalInArr = item.product_price * item.product_quantity;
          total.push(totalInArr);
        });
        totalPrice = total.reduce((a, b) => a + b);
      }
      this.cartTotalPrice = totalPrice;
      return totalPrice;
    },
  },
  methods: {
    async partnerAuthenticate() {
      try {
        let body = {
          access_token: this.access_token,
          secret_key: this.secret_key,
        };
        let get_customer_info = await Restful.post(
          `${APIBase}/v1/service/partner-authenticate`,
          body
        );
        if (
          get_customer_info.data.succes &&
          get_customer_info.data.code == 200
        ) {
          this.isOAuth = true;
          this.fullName =
            get_customer_info.data.data.public_profile.client_name;
        }
      } catch (error) {
        this.isOAuth = false;
        console.log("info err", error);
      }
    },
    // lấy accessTokenKioviet
    async getAccessToken() {
      if (!this.clientId) {
        Toast.fire({
          icon: "error",
          title: `Client Id chưa nhập thông tin`,
        });
      } else if (!this.clientSecret) {
        Toast.fire({
          icon: "error",
          title: `Client Secret chưa nhập thông tin`,
        });
      } else {
        let params = {
          client_id: this.clientId,
          client_secret: this.clientSecret,
        };
        try {
          let accessToken = await Restful.get(
            `${host}/selling-page/other/kiotviet_access_token`,
            params
          );
          this.accessTokenKiotviet = accessToken.data.data.access_token;
          localStorage.setItem("client_id", this.clientId);
          localStorage.setItem("client_secret", this.clientSecret);
          localStorage.setItem(
            "access_token_kiotviet",
            accessToken.data.data.access_token
          );
          this.checkIdAndSecret = false;
          this.checkStoreAndRetailer = true;
          Toast.fire({
            icon: "success",
            title: "Thành công",
          });
        } catch (err) {
          console.log(err);
          Toast.fire({
            icon: "error",
            title: "Vui lòng thử lại",
          });
        }
      }
    },
    // đồng bộ sản phẩm với store
    async syncProduct() {
      if (!this.storeId) {
        Toast.fire({
          icon: "error",
          title: `Store Id chưa nhập thông tin`,
        });
      } else if (!this.retailer) {
        Toast.fire({
          icon: "error",
          title: `Retailer chưa nhập thông tin`,
        });
      } else {
        let params = {
          store_id: this.storeId,
          Retailer: this.retailer,
          Authorization: localStorage.getItem("access_token_kiotviet"),
        };
        try {
          let syncProduct = Restful.get(
            `${host}/selling-page/product/sync_product_kiotviet`,
            params
          );
          localStorage.setItem("store_id", this.storeId);
          localStorage.setItem("retailer", this.retailer);
          Toast.fire({
            icon: "success",
            title: "Thành công",
          });
          this.checkStoreAndRetailer = false;
        } catch (err) {
          console.log(err);
          Toast.fire({
            icon: "error",
            title: "Vui lòng thử lại",
          });
        }
      }
    },
    // xác thực
    async runOAuth() {
      try {
        let body = {
          _type: "oauth-access-token",
          access_token: this.access_token,
          token_partner: "active",
        };
        let Oauth = await Restful.post(
          `${APIBase}/v1/app/app-installed/update`,
          body
        );
        if (Oauth) {
          this.isOAuth = true;
          window.close();
        }
      } catch (e) {
        console.log(e);
      }
    },
    //lấy chi nhánh
    async getBranch() {
      let params = {
        Retailer: localStorage.getItem("retailer"),
        Authorization: localStorage.getItem("access_token_kiotviet"),
      };
      try {
        let branch = await Restful.get(
          `${host}/selling-page/other/kiotviet_get_branch`,
          params
        );
        this.listBranch = branch.data.data.data;
      } catch (err) {
        console.log(err);
      }
    },
    //lấy dữ liệu tỉnh/tp
    async getCity() {
      try {
        let getCity = await Restful.get(
          `${host}/delivery/subvn/thongtin/tinhtp`
        );
        this.listCity = getCity.data.data;
      } catch (err) {
        console.log(err);
      }
    },
    //lấy dữ liệu quận/huyện
    async getDistrict() {
      let params = {
        matinh: this.city.code,
      };
      try {
        let city = await Restful.get(
          `${host}/delivery/subvn/thongtin/quanhuyen`,
          params
        );
        this.listDistrict = city.data.data;
      } catch (err) {
        console.log(err);
      }
    },
    // lấy dữ liệu xã/phường
    async getWard() {
      let params = {
        mahuyen: this.district.code,
      };
      try {
        let district = await Restful.get(
          `${host}/delivery/subvn/thongtin/xaphuonghuyen`,
          params
        );
        this.listWard = district.data.data;
      } catch (err) {
        console.log(err);
      }
    },
    // mở modal thêm sp vào giỏ hàng
    openModalAddCart() {
      this.getProductCart();
      this.isShowModalProduct = true;
    },
    // đóng modal thêm sp vào giỏ hàng
    closeAddProduct() {
      this.getCart();
      this.isShowModalProduct = false;
    },
    // lấy tất cả sp
    async getProductCart() {
      let params = {
        store_id: this.storeId,
      };
      try {
        let getProduct = await Restful.get(
          `${host}/selling-page/product/product_read`,
          params
        );
        this.listProduct = getProduct.data.data;
      } catch (err) {
        console.log(err);
      }
    },
    // thêm sản phẩm vào giỏ hàng
    async addProductForCart(item) {
      let body = {
        store_id: this.storeId,
        product_id: item.product_id,
        client_id: localStorage.getItem("client_id"),
        product_price: item.product_price,
        product_quantity: 1,
        product_name: item.product_name,
        other_info: item.image,
      };
      try {
        let addCart = await Restful.post(
          `${host}/selling-page/cart/cart_add_product`,
          body
        );
        Toast.fire({
          icon: "success",
          title: "Đã thêm vào giỏ hàng",
        });
      } catch (err) {
        console.log(err);
      }
    },
    //mở ghi chú
    openNote() {
      this.isShowModalNote = true;
    },
    //đóng ghi chú
    closeNote() {
      this.isShowModalNote = false;
    },
    // lấy dữ liệu giỏ hàng
    async getCart() {
      let body = {
        client_id: localStorage.getItem("client_id"),
        sort: "createdAt ASC",
      };
      try {
        let getCart = await Restful.post(
          `${host}/selling-page/cart/cart_read`,
          body
        );
        this.cart = getCart.data.data;
      } catch (err) {
        console.log(err);
      }
    },
    //tăng số lượng trong giỏ hàng
    async handleAddCart(id) {
      let body = {
        store_id: this.storeId,
        product_id: id,
        client_id: localStorage.getItem("client_id"),
      };
      try {
        let add = await axios.post(
          `${host}/selling-page/cart/cart_add_product`,
          body
        );
        this.getCart();
      } catch (err) {
        console.log(err);
      }
    },
    //giảm số lượng trong giỏ hàng
    async handleSubCart(id) {
      let body = {
        store_id: this.storeId,
        product_id: id,
        client_id: localStorage.getItem("client_id"),
      };
      try {
        let sub = await Restful.post(
          `${host}/selling-page/cart/cart_sub_product`,
          body
        );
        this.getCart();
      } catch (err) {
        console.log(err);
      }
    },
    //xóa 1 sp trong giỏ hàng
    async handleDeleteItemCart(id) {
      let body = {
        store_id: this.storeId,
        product_id: id,
        client_id: localStorage.getItem("client_id"),
      };
      let deleteItem = await Restful.post(
        `${host}/selling-page/cart/cart_delete_product`,
        body
      );
      this.getCart();
    },
    async deleteAllCart() {
      let body = {
        store_id: this.storeId,
        client_id: localStorage.getItem("client_id"),
      };
      try {
        let deleteCart = await Restful.post(
          `${host}/selling-page/cart/cart_delete`,
          body
        );
        this.getCart();
      } catch (err) {
        console.log(err);
      }
    },
    async createBill() {
      let address =
        this.address +
        " " +
        this.ward +
        " " +
        this.district.name +
        " " +
        this.city.name;
      let body = {
        store_id: this.storeId,
        branchId: this.branchId,
        product_info: this.cart,
        customer_name: this.fullName,
        customer_phone: this.phoneNumber,
        customer_address: address,
        customer_city_name: this.city.name,
        customer_district_name: this.district.name,
        note: this.note,
        Retailer: localStorage.getItem("retailer"),
        Authorization: localStorage.getItem("access_token_kiotviet"),
        // platform_type: "kiotviet",
      };
      if (!this.fullName) {
        Toast.fire({
          icon: "error",
          title: `Họ và tên chưa nhập thông tin`,
        });
      } else if (!this.phoneNumber) {
        Toast.fire({
          icon: "error",
          title: `Số điện thoại chưa nhập thông tin`,
        });
      } else if (!this.address) {
        Toast.fire({
          icon: "error",
          title: `Địa chỉ chưa nhập thông tin`,
        });
      } else if (!this.branchId) {
        Toast.fire({
          icon: "error",
          title: `Chi nhánh chưa nhập thông tin`,
        });
      } else if (!this.city.name) {
        Toast.fire({
          icon: "error",
          title: `Tỉnh/Thành phố chưa nhập thông tin`,
        });
      } else if (!this.district.name) {
        Toast.fire({
          icon: "error",
          title: `Quận/Huyện chưa nhập thông tin`,
        });
      } else if (!this.ward) {
        Toast.fire({
          icon: "error",
          title: `Xã/Phường chưa nhập thông tin`,
        });
      } else if (this.cart.length === 0) {
        Toast.fire({
          icon: "error",
          title: "Giỏ hàng trống",
        });
      } else {
        try {
          let createBill = await Restful.post(
            `${host}/selling-page/order/order_kiotviet`,
            body
          );
          this.phoneNumber = "";
          this.address = "";
          this.city = "";
          this.district = "";
          this.ward = "";
          this.branchId = "";
          Toast.fire({
            icon: "success",
            title: "Tạo đơn hàng thành công",
          });
          this.deleteAllCart();
          this.cart();
        } catch (err) {
          Toast.fire({
            icon: "error",
            title: err.data.error_message.message,
          });
        }
      }
    },
    reConfig() {
      localStorage.removeItem("client_id");
      localStorage.removeItem("client_secret");
      localStorage.removeItem("access_token_kiotviet");
      localStorage.removeItem("store_id");
      localStorage.removeItem("retailer");
      this.isConfig = false;
      this.checkIdAndSecret = true;
      this.checkStoreAndRetailer = false;
    },
  },
};
</script>

<style lang="scss" scope>
body {
  padding: 0;
  margin: 0;
  overflow-x: hidden;
  font-size: 13px;
  .btn-auth {
    position: absolute;
    top: 50%;
    left: 40%;
  }
  .content {
    .header {
      background-color: #dbd9d9;
      .content-header {
        h5 {
          font-size: 13px;
        }
        button {
          height: 30px;
          font-size: 13px;
          padding-top: 4px;
        }
      }
    }
    .body {
      padding: 5px 0;
      overflow: hidden;
      .add-product {
        display: inline-block;
        .modal-add-product {
          background-color: #fff;
          position: fixed;
          z-index: 2;
          bottom: 0;
          width: 100%;
          height: 100%;
          .btn-close {
            background-color: #777777;
            float: right;
            border: none;
            cursor: pointer;
            border-radius: 50%;
            font-weight: bold;
          }
        }
        .btn-show-modal {
          padding: 0;
          border: none;
          color: blue;
          background-color: #fff;
          span {
            font-size: 13px;
          }
        }
        .show-product {
          height: 100%;
          width: 100%;
          cursor: pointer;
          overflow-y: scroll;
          overflow-x: hidden;
          .row {
            margin-top: 5px;
            border-bottom: 1px solid #fff;
          }
          .col-2 {
            padding-right: 0;
            img {
              width: 45px;
              height: 45px;
            }
          }
          .col-9 {
            p {
              margin: 0;
              font-weight: bold;
              font-size: 13px;
            }
            span {
              font-size: 11px;
            }
          }
        }
        .show-product::-webkit-scrollbar {
          display: none;
        }
      }
      .btn-add-note {
        display: inline-block;
        button {
          padding: 0;
          border: none;
          color: blue;
          background-color: #fff;
          span {
            font-size: 13px;
          }
        }
      }
      .modal-note {
        margin-top: 5px;
      }
      .table {
        font-size: 12px;
        tr {
          td {
            padding: 0;
            margin: 0 2px;
            i {
              margin-top: 5px;
              text-align: center;
              background-color: #dbd9d9;
              cursor: pointer;
              font-size: 14px;
            }
            p {
              margin: 6px 2px 0 2px;
              padding: auto 0;
              text-align: center;
            }
          }
        }
        img {
          margin-top: 5px;
          width: 35px;
          height: 35px;
        }
        input {
          margin-top: 5px;
          text-align: center;
          padding: 0;
          width: 32px;
          height: 32px;
        }
        i {
          margin-top: 5px;
          margin-left: 1px;
          display: block;
        }
      }
    }
    .bill-detail {
      padding: 5px;
      margin-bottom: 8px;
      border: 1px solid #dedede;
      border-radius: 5px;
      font-size: 14px;
      p {
        margin: 0;
      }
      .bill-left {
        font-weight: bold;
      }
      .bill-right {
        text-align: right;
      }
    }
  }
  .modal-get-info {
    position: fixed;
    left: 0;
    top: 0;
    width: 100%;
    height: 100%;
    background-color: #fff;
    .form-get-info {
      margin-top: 10px;
      display: flex;
      justify-content: center;
      align-items: center;
      font-size: 12px;
      input {
        display: block;
        margin: 5px 0;
      }
      button {
        background-color: blue;
        color: #fff;
        cursor: pointer;
        border: none;
      }
    }
  }
  .modal-form-sync-product {
    position: fixed;
    left: 0;
    top: 0;
    width: 100%;
    height: 100%;
    background-color: #fff;
    .form-sync-product {
      margin-top: 10px;
      display: flex;
      justify-content: center;
      align-items: center;
      font-size: 12px;
      input {
        display: block;
        margin: 5px 0;
      }
      button {
        background-color: blue;
        color: #fff;
        cursor: pointer;
        border: none;
      }
    }
  }
}
</style>
