
### Tìm hiểu tất cả các hàm của Promise -> Thử liên tưởng thực tế và lấy 1 bài toán làm ví dụ cho các hàm này

#### Promise.all()
- params: mảng các Promise
- returns: nếu tất cả các Promise truyền vào được resolved, trả về 1 Promise trạng thái hoàn thành với giá trị là 1 mảng các giá trị của các Promise truyền vào. Nếu tồn tại 1 Promise bị rejected, trả về giá trị của Promise đầu tiền bị rejected
Ví dụ code:
```javascript
const p1 = new Promise((resolve, reject) => {
		resolve("Fulfilled 1")
	}
)
const p2 = new Promise((resolve, reject) => {
		resolve("Fulfilled 2")
	}
)
const p3 = new Promise((resolve, reject) => {
		reject(new Error("Rejected"))
	}
)

Promise.all([p1, p2]).then((values) => {
	console.log(values) // Output: ["Fulfilled1", "Fulfilled2"]
})

Promise.all([p1, p2, p3]).then((values) => {
	console.log(values) // Error vì có Promise bị rejected
})
```

Ví dụ thực tế: để cưới vợ và có 1 đám cưới hoàn chỉnh, chúng ta cần phải có đầy đủ các thứ như người yêu, nhẫn cưới, chụp ảnh cưới, cỗ bàn, tráp lễ ăn hỏi,... Nếu chỉ có người yêu mà không có tiền để chuẩn bị những thứ kể trên thì cũng không thể lấy vợ được. Thời đại bây giờ không tồn tại tư tưởng "1 túp lều tranh, 2 trái tim vàng". Bài học rút ra => Không có tiền, không yêu đương.

#### Promise.allSettled()
- params: mảng các Promise
- returns: khi tất cả các Promise truyền vào đều được xử lý (resolved hoặc rejected), trả về 1 Promise trạng thái hoàn thành với giá trị là 1 mảng Object mô tả kết quả của các Promise truyền vào
Ví dụ code:  
```javascript
Promise.allSettled([p1, p2, p3]).then((values) => {
	console.log(values)
	/*
		Output:
		[
			{ status: "fulfilled", value: "Fulfilled1" },
			{ status: "fulfilled", value: "Fulfilled2" },
			{ status: "rejected", reason: Error }
		]
	*/
})
```

#### Promise.any()
- params: mảng các Promise
- returns: 1 Promise trạng thái hoàn thành với giá trị là giá trị của Promise đầu tiên được resolved . Nếu không có Promise nào được resolved hoặc params là rỗng thì trả về lỗi
Ví dụ code:  
```javascript
Promise.any([p1, p2]).then((values) => {
	console.log(values) // Output: Fulfilled1
})

Promise.any([p3, p1]).then((values) => {
	console.log(values) // Output: Fulfilled1
})

Promise.any([p3]).then((values) => {
	console.log(values) // Error vì không có Promise nào được resolved
})

Promise.any([]).then((values) => {
	console.log(values) // Error vì params rỗng
})
```
Ví dụ thực tế: Sơn Tinh và Thủy Tinh, ai mang lễ vật tới trước thì được cưới Mị Nương
  

#### Promise.race()

param: 1 mảng các Promise
returns: 1 Promise với trạng thái là trạng thái của Promise đầu tiên trong mảng bị resolved/rejected
```javascript
const p1 = new Promise((resolve, reject) => {
	setTimeout(() => {
		resolve("DONE")
	}, 2000)
})
const p2 = new Promise((resolve, reject) => {
	setTimeout(() => {
		reject(new Error("An error occured"))
	}, 1000)
})

Promise.race([p1, p2]).then(result => {
	console.log(result) // Error bởi vì p2 được hoàn thành trước với status là 'rejected'
})
```

  

#### Promise.reject()
Trả về 1 Promise đã bị rejected
```javascript
Promise.reject("Rejected").catch(error => { console.log(error) }) // Output: Rejected
```

  

#### Promise.resolver()
Trả về 1 Promise đã được resolved
```javascript
Promise.resolve("Resolved").then(result=> { console.log(result) }) // Output: Resolved
```
  
  

### Các cách cancel 1 Promise
- Sử dụng 1 số thư viện bên ngoài
### Các promise phụ thuộc kết quả của nhau thì viết như thế nào? VD: gọi api đăng nhập, sau khi gọi đăng nhập thành công gọi api lấy thông tin user.
Có thể sử dụng callback, async/await hoặc thuộc tính 'then' của Promise để chờ API đầu tiên trả về kết quả, sau đó mới gọi API đằng sau


  
  

### Async / Await function kết hợp nhiều .then().then() .catch() ... xem các thức hoạt động và hiểu rõ hơn khi kết hợp
Từ khóa **async**  đặt trước 1 hàm làm cho hàm đó trở thành 1 hành động bất đồng bộ và trả về 1 Promise
```javascript
function myFunc(): Promise<string> {
	return Promise.resolve("Hello anh Trực")
}

async function myAsyncFunc(): Promise<string> {
	return "Hello anh Trực"
}
```
Sử dụng từ khóa **await** trước 1 hàm bất đồng bộ để chờ hàm đó thực hiện xong thì mới làm tiếp các hành động phía sau
Trường hợp sử dụng nhiều .then().then().catch() nếu các Promise cần dựa trên kết quả trả về của nhau để thực hiện. Sử dụng .catch() để xử lý lỗi nếu có Promise trong Promise chainning ném ra 1 Error
Ví dụ dễ thấy nhất là việc gọi API lấy thông tin user sau khi đăng nhập như trên
### Callback và biến Callback thành 1 Promise
**Callback** là 1 function được truyền dưới dạng đối số ở trong 1 function khác. Theo tiêu chuẩn, tham số đầu tiên của 1 callback function là error, tham số thứ 2 là kết quả của function có callback
Ví dụ:
```javascript
function addTwoNumber(a, b, callback) {
	callback(null, a + b)
}
addTwoNumber(1, 2, (err, value) => {
	if (err) console.log(err)
	console.log(value) // Output: 3
})


```
*Tại sao cần convert callback thành Promise?*
Nếu trong code có  nhiều callback lồng nhau, code trở nên rắc rối và khó đọc dẫn đến việc maintain trở nên khó khăn
Ví dụ về callback hell: 
```javascript
// Tính tổng từ 1 tới 5 bằng hàm addTwoNumber
addTwoNumber(1, 2, (err, value) => {
	if (err) console.log(err)
	addTwoNumber(value, 3, (err, value) => {
			if (err) console.log(err)
			console.log(value) // Output: 6
			// ...more and more callback here
		}
	)
})
```
*Convert callback to Promise*: làm cho code trở nên dễ đọc và dễ maintain hơn, tránh được việc rơi vào callback hell
```javascript
function promisify(fn) {
	return (...args) => {
		return new Promise((resolve, reject) => {
			fn(...args, (err, value) => {
				if (err) reject(err)
				resolve(value)
			})
		})
	}
}

const addTwoNumberPromise = promisify(addTwoNumber)

const testAddTwoNumberPromise = async () => {
	const firstSum = await addTwoNumberPromise(1, 2) // 3
	const secondSum = await addTwoNumberPromise(firstSum, 3) // 6
	const thirdSum = await addTwoNumberPromise(secondSum, 4) // 10
}
```

