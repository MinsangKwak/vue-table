<template>
	<div class="page-form-member">
		<nav class="main-navigation">
			<ul>
				<li>Menu Item 1</li>
				<li>Menu Item 2</li>
				<li>Menu Item 3</li>
				<li>Menu Item 4</li>
			</ul>
		</nav>
		<main class="main">
			<!-- s:form-navigation-header -->
			<div class="form-navigation-header">
				<div class="form-inner">
					<div class="select-container">
						<select
							name="status"
							id="status"
							v-model="filters.status"
						>
							<option value="">이용상태</option>
							<option value="all">전체</option>
							<option value="using">이용중</option>
							<option value="cancel-request">해지신청</option>
							<option value="cancel-complete">해지완료</option>
							<option value="refund-request">환불신청</option>
							<option value="refund-complete">환불완료</option>
							<option value="payment-pending">결제대기</option>
						</select>
					</div>

					<div class="radio-container">
						<span>개인정보 수집 동의</span>
						<label for="privacy-consent">동의</label>
						<input
							type="radio"
							id="privacy-consent"
							name="privacy-consent"
							value="agree"
							v-model="filters.privacyConsent"
						/>
						<label for="privacy-consent-disagree"
							>동의하지 않음</label
						>
						<input
							type="radio"
							id="privacy-consent-disagree"
							name="privacy-consent"
							value="disagree"
							v-model="filters.privacyConsent"
						/>
					</div>
				</div>

				<div class="form-inner">
					<div class="select-container">
						<select
							name="use-start-date"
							id="use-start-date"
							v-model="filters.useStartDate"
						>
							<option value="">이용시작일</option>
							<option value="">이용종료일</option>
						</select>
					</div>

					<div class="radio-container">
						<span class="radio-date">
							<label for="date-7-days">7일</label>
							<input
								type="radio"
								id="date-7-days"
								name="date-range"
								value="7-days"
								v-model="filters.dateRange"
								@change="updateDateRange('7-days')"
							/>
						</span>
						<span class="radio-date">
							<label for="date-1-month">1개월</label>
							<input
								type="radio"
								id="date-1-month"
								name="date-range"
								value="1-month"
								v-model="filters.dateRange"
								@change="updateDateRange('1-month')"
							/>
						</span>
						<span class="radio-date">
							<label for="date-3-months">3개월</label>
							<input
								type="radio"
								id="date-3-months"
								name="date-range"
								value="3-months"
								v-model="filters.dateRange"
								@change="updateDateRange('3-months')"
							/>
						</span>
						<span class="radio-date">
							<label for="date-6-months">6개월</label>
							<input
								type="radio"
								id="date-6-months"
								name="date-range"
								value="6-months"
								v-model="filters.dateRange"
								@change="updateDateRange('6-months')"
							/>
						</span>
						<span class="radio-date">
							<label for="date-1-year">1년</label>
							<input
								type="radio"
								id="date-1-year"
								name="date-range"
								value="1-year"
								v-model="filters.dateRange"
								@change="updateDateRange('1-year')"
							/>
						</span>
					</div>

					<div class="calendar-container">
						<flat-pickr
							v-model="filters.startDate"
							placeholder="YYYY/MM/DD"
						></flat-pickr>
						<flat-pickr
							v-model="filters.endDate"
							placeholder="YYYY/MM/DD"
						></flat-pickr>
					</div>
				</div>

				<div class="form-inner">
					<input
						type="text"
						name="search-query"
						placeholder="검색어를 입력하세요"
						v-model="filters.searchQuery"
					/>
					<button @click="search">검색</button>
					<button @click="reset">검색 초기화</button>
				</div>

				<div class="table-header">
					<div class="total">총 {{ totalItems }} 건</div>
					<div>
						<button @click="exportExcel">엑셀 다운로드</button>
						<select
							name="view-count"
							id="view-count"
							v-model="viewCount"
						>
							<option value="20">20개씩 보기</option>
							<option value="50">50개씩 보기</option>
							<option value="100">100개씩 보기</option>
						</select>
					</div>
				</div>
			</div>
			<!-- e:form-navigation-header -->
			<!-- s:table -->
			<table class="table">
				<thead>
					<tr>
						<th>No</th>
						<th>이름</th>
						<th>ID(이메일)</th>
						<th>휴대폰 번호</th>
						<th>부모님 휴대폰 번호</th>
						<th>상품명</th>
						<th>상품코드</th>
						<th>이용상태</th>
						<th>입력자</th>
						<th>서비스 신청일</th>
						<th>결과 처리 상태</th>
					</tr>
				</thead>
				<tbody>
					<tr v-for="(item, index) in paginatedItems" :key="index">
						<td>{{ item.no }}</td>
						<td>{{ item.name }}</td>
						<td>{{ item.email }}</td>
						<td>{{ item.phone }}</td>
						<td>{{ item.parentPhone }}</td>
						<td>{{ item.productName }}</td>
						<td>{{ item.productCode }}</td>
						<td>{{ item.status }}</td>
						<td>{{ item.author }}</td>
						<td>{{ item.applicationDate }}</td>
						<td>{{ item.resultStatus }}</td>
					</tr>
				</tbody>
			</table>
			<!-- e:table -->
			<div class="pagination">
				<span
					v-for="page in totalPages"
					:key="page"
					@click="goToPage(page)"
					>{{ page }}</span
				>
			</div>
		</main>
	</div>
</template>

<script>
import flatPickr from "vue-flatpickr-component";
import "flatpickr/dist/flatpickr.css";

export default {
	name: "FormMember",
	components: {
		flatPickr,
	},
	data() {
		return {
			items: [
				{
					no: 1,
					name: "John Doe",
					email: "john@example.com",
					phone: "010-1234-5678",
					parentPhone: "010-8765-4321",
					productName: "Product A",
					productCode: "A123",
					status: "이용중",
					author: "Admin",
					applicationDate: "2023/01/01",
					resultStatus: "완료",
				},
				{
					no: 2,
					name: "Jane Doe",
					email: "jane@example.com",
					phone: "010-2345-6789",
					parentPhone: "010-9876-5432",
					productName: "Product B",
					productCode: "B234",
					status: "해지완료",
					author: "Admin",
					applicationDate: "2023/02/01",
					resultStatus: "완료",
				},
				{
					no: 3,
					name: "Alice Johnson",
					email: "alice@example.com",
					phone: "010-3456-7890",
					parentPhone: "010-6543-2109",
					productName: "Product C",
					productCode: "C345",
					status: "환불완료",
					author: "Admin",
					applicationDate: "2023/03/01",
					resultStatus: "완료",
				},
				{
					no: 4,
					name: "Bob Smith",
					email: "bob@example.com",
					phone: "010-4567-8901",
					parentPhone: "010-7654-3210",
					productName: "Product D",
					productCode: "D456",
					status: "결제대기",
					author: "Admin",
					applicationDate: "2023/04/01",
					resultStatus: "완료",
				},
				{
					no: 5,
					name: "Charlie Brown",
					email: "charlie@example.com",
					phone: "010-5678-9012",
					parentPhone: "010-8765-4321",
					productName: "Product E",
					productCode: "E567",
					status: "이용중",
					author: "Admin",
					applicationDate: "2023/05/01",
					resultStatus: "완료",
				},
				{
					no: 6,
					name: "David Wilson",
					email: "david@example.com",
					phone: "010-6789-0123",
					parentPhone: "010-9876-5432",
					productName: "Product F",
					productCode: "F678",
					status: "해지신청",
					author: "Admin",
					applicationDate: "2023/06/01",
					resultStatus: "완료",
				},
				{
					no: 7,
					name: "Eva Green",
					email: "eva@example.com",
					phone: "010-7890-1234",
					parentPhone: "010-0987-6543",
					productName: "Product G",
					productCode: "G789",
					status: "해지완료",
					author: "Admin",
					applicationDate: "2023/07/01",
					resultStatus: "완료",
				},
				{
					no: 8,
					name: "Frank White",
					email: "frank@example.com",
					phone: "010-8901-2345",
					parentPhone: "010-1098-7654",
					productName: "Product H",
					productCode: "H890",
					status: "환불신청",
					author: "Admin",
					applicationDate: "2023/08/01",
					resultStatus: "완료",
				},
				{
					no: 9,
					name: "Grace Lee",
					email: "grace@example.com",
					phone: "010-9012-3456",
					parentPhone: "010-2109-8765",
					productName: "Product I",
					productCode: "I901",
					status: "이용중",
					author: "Admin",
					applicationDate: "2023/09/01",
					resultStatus: "완료",
				},
				{
					no: 10,
					name: "Henry Brown",
					email: "henry@example.com",
					phone: "010-0123-4567",
					parentPhone: "010-3210-9876",
					productName: "Product J",
					productCode: "J012",
					status: "결제대기",
					author: "Admin",
					applicationDate: "2023/10/01",
					resultStatus: "완료",
				},
			],
			currentPage: 1,
			viewCount: 20,
			filters: {
				status: "",
				privacyConsent: "",
				useStartDate: "",
				dateRange: "",
				startDate: new Date().toISOString().substr(0, 10),
				endDate: new Date().toISOString().substr(0, 10),
				searchQuery: "",
			},
			totalPages: 1,
		};
	},
	computed: {
		filteredItems() {
			// 'all' 선택 시 모든 아이템을 보여줌
			if (this.filters.status === 'all' || this.filters.status === '') {
				return this.items;
			} else {
				// 그 외에는 선택된 status와 일치하는 아이템만 필터링
				return this.items.filter(item => item.status === this.filters.status);
			}
		},
		paginatedItems() {
			const start = (this.currentPage - 1) * this.viewCount;
			const end = this.currentPage * this.viewCount;
			return this.items.slice(start, end);
		},
		totalItems() {
			return this.items.length;
		},
	},
	methods: {
		search() {
			// 검색 기능 구현
			console.log("검색 기능 실행");
		},
		reset() {
			// 검색 초기화 기능 구현
			this.filters = {
				status: "",
				privacyConsent: "",
				useStartDate: "",
				dateRange: "",
				startDate: new Date().toISOString().substr(0, 10),
				endDate: new Date().toISOString().substr(0, 10),
				searchQuery: "",
			};
			console.log("검색 필터 초기화");
		},
		exportExcel() {
			// 엑셀 다운로드 기능 구현
			console.log("엑셀 다운로드 기능 실행");
		},
		goToPage(page) {
			this.currentPage = page;
			console.log(`페이지 이동: ${page}`);
		},
		updateDateRange(range) {
			const startDate = new Date(this.filters.startDate);
			let endDate = new Date(startDate);

			switch (range) {
				case "7-days":
					endDate.setDate(startDate.getDate() + 7);
					break;
				case "1-month":
					endDate.setMonth(startDate.getMonth() + 1);
					break;
				case "3-months":
					endDate.setMonth(startDate.getMonth() + 3);
					break;
				case "6-months":
					endDate.setMonth(startDate.getMonth() + 6);
					break;
				case "1-year":
					endDate.setFullYear(startDate.getFullYear() + 1);
					break;
			}

			this.filters.endDate = endDate.toISOString().substr(0, 10);
		},
	},
	mounted() {
		// 초기 로드시 현재 날짜로 설정
		const today = new Date().toISOString().substr(0, 10);
		this.filters.startDate = today;
		this.filters.endDate = today;
	},
};
</script>

<style scoped>
* {
	margin: 0;
	padding: 0;
	box-sizing: border-box;
}

.page-form-member {
	display: grid;
	grid-template-columns: 120px 1fr;
	gap: 10px;
	border: 1px solid black;
}

.main-navigation {
	border: 1px solid green;
}
.main {
	border: 1px solid blue;
}
.form-navigation-header {
	border: 1px solid black;
}

.form-inner {
	display: flex;
	border: 1px solid black;
}

.table-header {
	display: flex;
	justify-content: space-between;
}
</style>
