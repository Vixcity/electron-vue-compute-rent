<template>
  <div class="home">
    <div class="container">
      <div class="block">
        <span class="demonstration"
          >请选择要导出的月份（请勿选择跨年月份）</span
        >
        <el-date-picker
          v-model="chooseMonth"
          type="monthrange"
          align="right"
          unlink-panels
          range-separator="至"
          start-placeholder="开始月份"
          end-placeholder="结束月份"
          @change="changeMonth"
          :picker-options="pickerOptions"
          :clearable="false"
          format="yyyy-MM"
          value-format="yyyy-MM"
          style="margin-right: 10px"
        >
        </el-date-picker>
        <el-button type="primary">导出</el-button>
      </div>
      <el-table
        ref="table"
        :data="tableData"
        border
        style="width: 100%; margin-top: 20px"
      >
        <el-table-column
          type="index"
          label="序号"
          width="50"
          fixed="left"
        ></el-table-column>
        <el-table-column label="合同号" width="180">
          <template slot-scope="scope">
            <el-input
              v-model="scope.row.contractNumber"
              placeholder="输入合同号"
            ></el-input>
          </template>
        </el-table-column>
        <el-table-column label="承租户" width="180">
          <template slot-scope="scope">
            <el-input
              v-model="scope.row.tenant"
              placeholder="输入承租户"
            ></el-input>
          </template>
        </el-table-column>
        <el-table-column label="地址" width="180">
          <template slot-scope="scope">
            <el-input
              v-model="scope.row.address"
              placeholder="输入租户地址"
            ></el-input>
          </template>
        </el-table-column>
        <el-table-column prop="name" label="本期房租" width="180">
          <template slot-scope="scope">
            <el-input
              v-model="scope.row.rent"
              type="number"
              placeholder="输入本期房租"
              @input="changeDiffDay(scope.row)"
            ></el-input>
          </template>
        </el-table-column>
        <el-table-column prop="name" label="合同起始日期" width="180">
          <template slot-scope="scope">
            <el-date-picker
              v-model="scope.row.startDate"
              type="date"
              format="yyyy-MM-dd"
              value-format="yyyy-MM-dd"
              placeholder="选择起始日期"
              :clearable="false"
              style="width: 100%"
              @change="changeDiffDay(scope.row)"
            >
            </el-date-picker>
          </template>
        </el-table-column>
        <el-table-column prop="name" label="合同终止日期" width="180">
          <template slot-scope="scope">
            <el-date-picker
              v-model="scope.row.endDate"
              type="date"
              format="yyyy-MM-dd"
              value-format="yyyy-MM-dd"
              placeholder="选择终止日期"
              :clearable="false"
              style="width: 100%"
              @change="changeDiffDay(scope.row)"
            >
            </el-date-picker>
          </template>
        </el-table-column>
        <el-table-column prop="diffDay" label="合同天数" width="180">
        </el-table-column>
        <el-table-column prop="name" label="日租金金额（元）" width="180">
          <template slot-scope="scope">
            {{
              scope.row.diffDay
                ? ((scope.row.rent || 0) / scope.row.diffDay).toFixed(2)
                : 0
            }}
          </template>
        </el-table-column>
        <el-table-column
          v-for="(item, index) in monthArr"
          :key="index + 'monthArr'"
          :label="item.label"
          :prop="item.attribute"
          width="180"
        >
        </el-table-column>
        <el-table-column prop="name" label="合计权责金额" width="180">
        </el-table-column>
        <el-table-column
          prop="totalPrice"
          :label="chooseYear + '年度金额（元）'"
          width="180"
        >
        </el-table-column>
        <el-table-column fixed="right" label="操作" width="120">
          <template slot-scope="scope">
            <el-button
              type="primary"
              icon="el-icon-plus"
              circle
              @click="addTableData"
            ></el-button>
            <el-button
              type="danger"
              icon="el-icon-delete"
              :disabled="scope.row.id === 0"
              circle
              @click="deleteTableData(scope.row)"
            ></el-button>
          </template>
        </el-table-column>
      </el-table>
    </div>
  </div>
</template>

<script>
export default {
  name: "HomeView",
  data() {
    return {
      chooseYear: "",
      chooseMonth: [],
      tableData: [
        {
          contractNumber: "",
          tenant: "",
          address: "",
          rent: "",
          startDate: "",
          endDate: "",
          diffDay: 0,
          id: 0,
        },
      ],
      monthArr: [],
      pickerOptions: {
        shortcuts: [
          {
            text: "本月",
            onClick(picker) {
              picker.$emit("pick", [new Date(), new Date()]);
            },
          },
          {
            text: "第一季度",
            onClick(picker) {
              const end = new Date(new Date().getFullYear(), 2);
              const start = new Date(new Date().getFullYear(), 0);
              picker.$emit("pick", [start, end]);
            },
          },
          {
            text: "第二季度",
            onClick(picker) {
              const end = new Date(new Date().getFullYear(), 5);
              const start = new Date(new Date().getFullYear(), 3);
              picker.$emit("pick", [start, end]);
            },
          },
          {
            text: "第三季度",
            onClick(picker) {
              const end = new Date(new Date().getFullYear(), 8);
              const start = new Date(new Date().getFullYear(), 6);
              picker.$emit("pick", [start, end]);
            },
          },
          {
            text: "第四季度",
            onClick(picker) {
              const end = new Date(new Date().getFullYear(), 11);
              const start = new Date(new Date().getFullYear(), 9);
              picker.$emit("pick", [start, end]);
            },
          },
          {
            text: "上半年",
            onClick(picker) {
              const end = new Date(new Date().getFullYear(), 5);
              const start = new Date(new Date().getFullYear(), 0);
              picker.$emit("pick", [start, end]);
            },
          },
          {
            text: "下半年",
            onClick(picker) {
              const end = new Date(new Date().getFullYear(), 11);
              const start = new Date(new Date().getFullYear(), 6);
              picker.$emit("pick", [start, end]);
            },
          },
          {
            text: "今年",
            onClick(picker) {
              const end = new Date(new Date().getFullYear(), 11);
              const start = new Date(new Date().getFullYear(), 0);
              picker.$emit("pick", [start, end]);
            },
          },
          {
            text: "去年",
            onClick(picker) {
              const end = new Date(
                (new Date().getFullYear() - 1).toString(),
                11
              );
              const start = new Date((new Date().getFullYear() - 1).toString());
              picker.$emit("pick", [start, end]);
            },
          },
        ],
      },
      enMonth: [
        "Jan",
        "Feb",
        "Mar",
        "Apr",
        "May",
        "Jun",
        "Jul",
        "Aug",
        "Sept",
        "Oct",
        "Nov",
        "Dec",
      ],
    };
  },
  methods: {
    // 更改月份
    changeMonth(e) {
      this.chooseYear = e[0].split("-")[0];
      this.chooseMonth = e;
      this.changeMonthArr();
    },

    // 更改表头
    changeMonthArr() {
      /**
       * @description 遍历选择月份，写出对应的数组
       * 数组格式 ↓
       * @returns {
       *  label:标题,
       *  dateLen: 当月日期天数,
       *  month: 月份
       * }
       * */
      let startMonth = this.chooseMonth[0].split("-")[1];
      let endMonth = this.chooseMonth[1].split("-")[1];
      this.monthArr = [];

      // 表头赋值
      for (let i = Number(startMonth); i < Number(endMonth) + 1; i++) {
        let strMonth = "";
        strMonth = i < 10 ? "0" + i : i;

        // 数组赋值
        this.monthArr.push({
          label: `${i}月权责金额[${i}月权责天数（ ${this.chooseYear}.${i}.01-${
            this.chooseYear
          }.${i}.${this.$moment(
            this.chooseYear + "-" + strMonth,
            "YYYY-MM"
          ).daysInMonth()}）]`,
          attribute: this.enMonth[i - 1],
        });
      }

      this.tableData.forEach((item) => {
        this.changeDiffDay(item);
      });
      // 刷新一下页面
      this.$forceUpdate();
    },

    // 计算每月权责金额
    getMonthResMoney(e) {
      let startIndex = "",
        endIndex = "";
      for (let i = 0; i < 12; i++) {
        let str = this.enMonth[i];
        let strMonth = "";
        strMonth = i + 1 < 10 ? "0" + (i + 1) : i + 1;

        // 对应的月份有几天
        let daysLen = this.$moment(
          this.chooseYear + "-" + strMonth,
          "YYYY-MM"
        ).daysInMonth();

        // 每月的开始日期和结束日期
        let startMoment = `${this.chooseYear}-${strMonth}-01`,
          endMoment = `${this.chooseYear}-${strMonth}-${daysLen}`;

        // 如果起始日期在之前，那么设置startIndex为负数
        if (new Date(e.startDate) < new Date(this.chooseYear + "-01-01")) {
          startIndex = -1;
        }

        // 起始日期是否在该月之内
        let startInMonth = this.isDuringDate(
          startMoment,
          endMoment,
          e.startDate
        );
        // 结束日期是否在该月之内
        let endInMonth = this.isDuringDate(startMoment, endMoment, e.endDate);

        // 起始日期结束日期都在该月
        if (startInMonth && endInMonth) {
          // 起始日期，结束日期都在里面 => 该月权责金额 = 计算起始日期到结束日期有多少天 * 金额
          e[str] = ((Number(e.rent) / e.diffDay) * e.diffDay).toFixed(2);
          startIndex = i;
          endIndex = i;
        } else if (startInMonth) {
          // 仅起始日期在里面 => 该月权责金额 = (每月月底的天数 - 起始日期 + 1) * 租金

          // 本月权责天数
          let monthDiffDay = daysLen - Number(e.startDate.split("-")[2]) + 1;
          e[str] = ((Number(e.rent) / e.diffDay) * monthDiffDay).toFixed(2);
          startIndex = i;
        } else if (endInMonth) {
          // 仅结束日期在里面 => 该月权责金额 = 结束日期 * 租金

          // 本月权责天数
          let monthDiffDay = Number(e.endDate.split("-")[2]);
          e[str] = ((Number(e.rent) / e.diffDay) * monthDiffDay).toFixed(2);
          endIndex = i;
        } else if (
          startIndex &&
          startIndex < i &&
          (endIndex > i || endIndex === "")
        ) {
          // 在起始日期和结束日期中间 => 该月权责金额 = 天数 * 租金

          e[str] = ((Number(e.rent) / e.diffDay) * daysLen).toFixed(2);
        } else {
          console.log(startIndex, endIndex, i);
          e[str] = "0.00";
        }
      }

      e.totalPrice = 0;
      this.enMonth.forEach((item) => {
        e.totalPrice += Number(e[item]);
      });
      e.totalPrice = e.totalPrice.toFixed(2);
    },

    // 判断某日期是否在某个时间范围内
    isDuringDate(beginDateStr, endDateStr, needDate) {
      let curDate = new Date(needDate),
        beginDate = new Date(beginDateStr),
        endDate = new Date(endDateStr);
      if (curDate >= beginDate && curDate <= endDate) {
        return true;
      }
      return false;
    },

    /**
     * @description 获取两个日期之差，并且把改动的值，写进参数里面
     * @param {
     *  startTime: string -> "YYYY-MM-DD",
     *  endTime: string -> "YYYY-MM-DD"
     * }
     */
    changeDiffDay(e) {
      if (!e.startDate || !e.endDate) return;
      e.diffDay = this.$moment(e.endDate).diff(e.startDate, "days") + 1;
      if (e.rent) {
        this.getMonthResMoney(e);
      }
      this.$forceUpdate();
    },

    // 添加一行
    addTableData() {
      this.tableData.push({ id: this.tableData.length });
    },

    // 删除一行
    deleteTableData(e) {
      let index = this.tableData.findIndex((item) => item.id === e.id);
      this.tableData.splice(index, 1);
    },
  },
  mounted() {
    this.chooseYear = new Date().getFullYear();
    this.chooseMonth = [
      this.$moment(new Date()).format("YYYY-01"),
      this.$moment(new Date()).format("YYYY-12"),
    ];
    this.changeMonthArr();
  },
};
</script>

<style lang="less" scoped>
.container {
  padding: 10px;
  .demonstration {
    margin-right: 10px;
  }
}
</style>
