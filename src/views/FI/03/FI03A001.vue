<template>
  <kb-page page-title="보험">
    <kb-page-body>
      <div class="section financial_bank_wrap">
        <!-- 보험정보가 없는 경우 -->
        <template v-if="noDate">
          <div class="assets_none_box">
            <div class="none_box">
              <i></i>
              이런, 보험 정보가 없네요!<br />
              보험정보를 연동해 주세요 :)
              <kb-button line blue class="more fz_16">보험정보 연동</kb-button>
            </div>
          </div>
        </template>
        <!-- //보험정보가 없는 경우 -->

        <div class="flex_wrap space_between align_center mg_b12">
          <div>
            <div v-if="true" class="error_txt icon mg_t0">연동 실패</div>
            <kb-button not aTag v-if="false" class="error_txt icon mg_t0">연결 정보를 확인하세요.<i aria-hidden="true" class="bt_ic_arr"></i></kb-button>
            <kb-button not aTag v-if="false" class="error_txt icon mg_t0">금융사 약관 동의가 필요합니다.<i aria-hidden="true" class="bt_ic_arr"></i></kb-button>
            <kb-button not aTag v-if="false" class="error_txt icon mg_t0">연동 유효기간이 만료되었습니다.<i aria-hidden="true" class="bt_ic_arr"></i></kb-button>
          </div>
          <div>
            <kb-button button class="refresh" @click="btnRefresh">09:00:03</kb-button>
          </div>
        </div>

        <!--2021-06-21  전체 내역이 없을때 -->
        <div v-if="false" class="no_list_txt icon h_full">
          <strong class="tit">내역이 없습니다.</strong>
        </div>
        <!-- //2021-06-21 전체 내역이 없을때 -->

        <div class="financial_bank_top">

          <h2>보험 가입 현황</h2>

          <kb-button not aTag class="money_box">
            <p class="text">월 납입액</p>
            <p class="money">123,400,000원</p>

            <ul class="info_text">
              <li><span>보험사</span>4개</li>
              <li><span>내 보험</span>32개</li>
            </ul>
          </kb-button>

          <div class="comment">자동차 보험이 30일 이내 만기 예정입니다.</div>

          <div class="chart_area mg_t24 t_center">
            <img src="~@/assets/images/temp/sample_insurance.png" alt="" />
          </div>

        </div>

        <hr aria-hidden="true" class="hr_line mg_t20">

        <div class="insurance_list">
          <kb-button aTag not>
            <div class="cont">
              <!-- 이슈 발생시 issue 클래스 적용  -->
              <i class="ico issue"><span style="background: skyblue;"></span></i>
              <div class="name">
                <p>KB Star 정기예금</p>
                <span>12345678901234</span>
              </div>
              <p class="tag">여행자 보험</p>
            </div>
          </kb-button>
          <kb-button aTag not>
            <div class="cont">
              <!-- 이슈 발생시 issue 클래스 적용  -->
              <i class="ico"><span style="background: skyblue;"></span></i>
              <div class="name">
                <p>KB Star 정기예금</p>
                <span>12345678901234</span>
              </div>
              <p class="tag">자동차보험 보험</p>
            </div>

            <div class="flex_wrap mg_l40 mg_t15">
              <p class="fz_14 fw_bold flex11">01가1234</p>
              <p class="fz_14 fw_bold">D-360</p>
            </div>
          </kb-button>
          <kb-button aTag not>
            <div class="cont">
              <!-- 이슈 발생시 issue 클래스 적용  -->
              <i class="ico"><span style="background: skyblue;"></span></i>
              <div class="name">
                <p>KB Star 정기예금</p>
                <span>12345678901234</span>
              </div>
              <p class="tag">자동차보험 보험</p>
            </div>

            <!-- 만약 아이콘이 없다면, mg_l40 은 삭제 -->
            <div class="ico_wrap mg_t4 mg_l40">
              <div>
                <span class="mg_r4 ico_txt pink">변액</span>
                <span class="mg_r4 ico_txt blue">유니버셜</span>
              </div>
            </div>

            <div class="money_info mg_l40">
              <p class="progress">10% 납입</p>
              <p class="money">
                <span>월</span>
                123,456,000원
              </p>
            </div>
            <div class="progress_bar mg_l40">
              <span style="width:10%"></span>
            </div>
          </kb-button>
          <kb-button aTag not>
            <div class="cont">
              <!-- 이슈 발생시 issue 클래스 적용  -->
              <i class="ico"><span style="background: skyblue;"></span></i>
              <div class="name">
                <p>KB Star 정기예금</p>
                <span>12345678901234</span>
              </div>
              <p class="tag">자동차보험 보험</p>
            </div>

            <div class="money_info mg_l40">
              <p class="money">
                <span>월</span>
                123,456,000원
              </p>
            </div>
          </kb-button>
          <kb-button aTag not>
            <div class="cont">
              <!-- 이슈 발생시 issue 클래스 적용  -->
              <i class="ico"><span style="background: skyblue;"></span></i>
              <div class="name">
                <p>KB Star 정기예금</p>
                <span>12345678901234</span>
              </div>
              <p class="tag">자동차보험 보험</p>
            </div>

            <div class="money_info mg_l40">
              <p class="money">
                <span>계좌평가금액</span>
                123,456,000원
              </p>
              <p class="money small">
                <span>적립금액</span>
                123,456,000원
              </p>
            </div>
          </kb-button>
          <!-- 2021-06-28 대출 타입 추가 -->
          <kb-button aTag not>
            <div class="cont">
              <!-- 이슈 발생시 issue 클래스 적용  -->
              <i class="ico"><span style="background: skyblue;"></span></i>
              <div class="name">
                <p>IRP 퇴직연금</p>
                <span>삼성생명 12345678901234567890</span>
              </div>
            </div>

            <div class="money_info mg_l40">
              <p class="money">
                <span>계좌평가금액</span>
                123,456,000원
              </p>
              <p class="money small">
                <span>적립금액</span>
                123,456,000원
              </p>
            </div>
          </kb-button>

          <kb-button aTag not>
            <div class="cont">
              <!-- 이슈 발생시 issue 클래스 적용  -->
              <i class="ico"><span style="background: skyblue;"></span></i>
              <div class="name">
                <p>IRP 퇴직연금</p>
                <span>삼성생명 12345678901234567890</span>
                <span>거치식 만기지정상환 - 원금균등분할상환</span>
              </div>
              <p class="tag">보증보험</p>
            </div>

            <div class="money_info mg_l40">
              <p class="money">
                <span>대출잔액</span>
                123,456,000원
              </p>
              <p class="money small">
                <span>다음 이자 상환일</span>
                2021/12/31
              </p>
              <p class="money small">
                <span>만기일</span>
                2021/12/31
              </p>
            </div>
          </kb-button>
          <!-- //2021-06-28 대출 타입 추가 -->
        </div>

        <!-- 하단배너 -->
        <div class="img_banner_box mg_t32">
          <kb-button not>
            <img src="/images/banner/sample_banner_2.png" alt="">
          </kb-button>
          <kb-button not>
            <img src="/images/banner/sample_banner_2.png" alt="">
          </kb-button>
          <kb-button not>
            <img src="/images/banner/sample_banner_2.png" alt="">
          </kb-button>
          <kb-button not>
            <img src="/images/banner/sample_banner_2.png" alt="">
          </kb-button>
          <kb-button not>
            <img src="/images/banner/sample_banner_2.png" alt="">
          </kb-button>
        </div>
        <!-- //하단배너 -->
      </div>
    </kb-page-body>
  </kb-page>
</template>
<script>
export default {
  name: 'FI03A001',
  data() {
    return {
      noDate: false,
      tabIdx: 0,
      isStar1: true,
      isStar2: false,
    };
  },
  watch: {
  },
  beforeMount() {
  },
  mounted() {
  },
  methods: {
    btnRefresh() {
      this.$loading(true, '불러오는중', 'C');
      setTimeout(() => {
        this.$loading(false);
      }, 2000);
    },
  },
};
</script>
