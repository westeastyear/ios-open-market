# 🏬 오픈마켓 I, II
> 프로젝트 기간: 2022.05.09 ~ 2022.06.03 <br>
> 팀원: [Donnie](https://github.com/westeastyear), [우롱차](https://github.com/dnwhd0112)
> 리뷰어: [또치](https://github.com/TTOzzi)

## 🔎 프로젝트 소개
**"서버에서 제공하는 `API`를 받아와서 사용자에게 원하는 형식(LIST, GRID)으로 뷰를 그려 상품의 정보를 제공하는 프로젝트"**

## 📺 프로젝트 실행화면
|레이아웃변경(List, Grid)|사진등록|pull to refresh|
|:---:|:---:|:---:|
|<img src="https://user-images.githubusercontent.com/74251593/185878258-82b1d822-71f7-48f4-af3c-449174fe58eb.gif">|<img src="https://user-images.githubusercontent.com/74251593/185881045-ca366cd4-d44a-4661-8ec7-07d6bf93ce48.gif">|<img src="https://user-images.githubusercontent.com/74251593/185883527-020628c8-e8a9-4897-b845-6d22e036b2f4.gif">|

|상품등록|DetailView이동|DetailView 구성|
|:---:|:---:|:---:|
|<img src="https://user-images.githubusercontent.com/74251593/185882568-fc96f293-9ccf-4e18-8c8b-691086b4e72b.gif">|<img src="https://user-images.githubusercontent.com/74251593/185879008-0f415188-2840-4bfd-8ce3-f874d3ceb43b.gif">|<img src="https://user-images.githubusercontent.com/74251593/185880509-437c579a-0ed8-4169-8cb6-4014a8a90384.gif">|

<br>

## 👀 PR

### 오픈마켓 I

[STEP 1](https://github.com/yagom-academy/ios-open-market/pull/140)

[STEP 2](https://github.com/yagom-academy/ios-open-market/pull/148)

### 오픈마켓 II

[STEP 1](https://github.com/yagom-academy/ios-open-market/pull/162)

[STEP 2](https://github.com/yagom-academy/ios-open-market/pull/171)

<br>

## 🛠 개발환경 및 라이브러리
- [![swift](https://img.shields.io/badge/swift-5.0-orange)]()
- [![xcode](https://img.shields.io/badge/Xcode-13.1-blue)]()
- [![iOS](https://img.shields.io/badge/iOS-15.0-red)]()

<br>

## 🔑 키워드

- `JSONDecoder`
- `CodingKeys`
- `URLSession`
- `URLSessionDataTask`
- `LocalizedError`
- `UISegmentedControl`
- `UICollectionView`
    - `UICollectionViewFlowLayout`
    - `UICollectionLayoutListConfiguration`
    - `UICollectionViewCompositionalLayout` 
    - `setCollectionViewLayout`
    - `reloadData`
- `Modern cell configuration`
    - `UICollectionViewDiffableDataSource`
    - `NSDiffableDataSourceSnapshot` 
- `Lists in UICollectionView`
    - `UIListContentConfiguration` 
    - `UIListContentView`
- `UIActivityIndicatorView`
- `NSMutableAttributedString`
- `prepareForReuse`
- `activityIndicator`
- `multipart-form`

<br>

## 📑 구현내용

- `Modern cell configuration` 방식으로 `List` 구현
- `session`을 이용하여 페이지 정보를 불러와서 `snapshot`에 저장 및 `dataSource`에 적용
- 화면에 보여줄 `collectionView`에서 셀을 생성한뒤 `dataSource`에 있는 정보들을 할당
- `Storyboard`없이 코드를 이용하여 `UI`구현
- `Mock`객체를 사용하여 `Network` UnitTest 진행 
- `NSMutableAttributedString`을 확장하여 원하는 부분에만 변경된 색상적용
- `CodingKeys`와 `init(from: decoder)`을 사용하여 디코딩
- `session`과 `networkable`을 프로토콜로 선언하여 활용 `session.dataTask`를 활용하여 서버와 통신하고 `completeHandler`와 `errorHandler`를 사용하여 비동기로 통신
- `completeHandler`와 `errorHandler`에 `Mock Session`으로부터 특정값을 전달하는 방식으로 진행
- `multipart-form`을 활용하여 이미지/텍스트 데이터 `POST`

<br>

## 📖 학습내용
- `Mock` 테스트 방식에 대한 이해
- `Session`을 활용한 네트워크 통신 방식 및 `completeHandler`를 사용하여 얻어온 데이터를 처리하는 방식에 대한 이해
- `CollectionView` 및 `CollectionViewListCell`을 구현 및 설계하는 방식에 대한 이해
- `activityIndicator`를 활용하여 로딩중을 표시하는 방법에 대한 이해
- `CollectionViewCell`에서 `prepareForReuse()`를 오버라이드 하여 재사용 셀을 초기화
- `multipart-form`의 데이터 전달 형식의 대한 이해 

<br>

## 🧐 STEP별 고민한 점 및 해결한 방법

## 오픈마켓 I [[STEP 1]](https://github.com/yagom-academy/ios-open-market/pull/140)
### 1. `requestData` 구현시 어디까지 `escaping Handler`에서 처리할것이고, 어디까지 함수 내부에서 처리를 한 뒤 `Handler`에게 넘겨줄지를 고민하였습니다.
> - 의논 결과 전부 다 `Handler`에서 처리하도록 하자고하여 `data`, `response`, `error`를 전부 다 넘겼습니다.
```swift
func requestData(url: String, completeHandler: @escaping (Data?, URLResponse?, Error?) -> Void ) {
        let configure = URLSessionConfiguration.default
        let session = URLSession(configuration: configure)
        
        let urlComponents = URLComponents(string: url)
        
        guard let requestURL = urlComponents?.url else { return }
        let dataTask = session.dataTask(with: requestURL) { (data, response, error) in
            guard error == nil else { return }
            completeHandler(data, response, error)
        }
```

<br>

### 2. `URL` 주소를 관리하는 방법에 대해
> - 처음엔 함수에서 `query` 매개변수를 받아 `URLQueryItem`으로 `URL`를 관리하는 방법을 채택했었는데, 어떤 `query` 파라미터를 사용해야 하는지 알기 어려워져서 다른 방법을 사용하게 되었습니다.
*(아무 String 값이 들어갈 수 있어서 새로 작성하는 입장에서는 어떤 값이 들어가는지 모르고 오류가 나기 쉽기 때문에 `enum`으로 관리하도록 변경하였습니다.)*
```swift
func inquireProductList(url: String, query: [(String, String)], completeHandler: @escaping (Data?, URLResponse?, Error?) -> Void ) {
    
------중략------
    
    var urlComponents = URLComponents(string: url)
    for item in query {
        let urlQueryItem = URLQueryItem(name: item.0, value: item.1)
        urlComponents?.queryItems?.append(urlQueryItem)
    }
}
```

> - 팀원과 `URL` 주소를 관리하는 법에 대하여 고민하였고, `enum`으로 관리해주기로 결정하였습니다.
```swift
enum OpenMarketApi {
    private static let hostUrl = "https://market-training.yagom-academy.kr/"
    
    case pageInformation(pageNo: Int, itemsPerPage: Int)
    case productDetail(productNumber: Int)
    
    var string: String {
        switch self {
        case .pageInformation(let pageNo, let itemsPerPage):
            return Self.hostUrl + "api/products?page_no=\(pageNo)&items_per_page=\(itemsPerPage)"
        case .productDetail(let productNumber):
            return Self.hostUrl + "api/products/\(productNumber)"
        }
    }
}
```

---

<br>

## [[STEP 2]](https://github.com/yagom-academy/ios-open-market/pull/148)

### 1. `ListCell`을 구현하기 위해 `customCell`을 만들어 등록하는 방식과, `Modern cell configuration` 방식중에 어떤것을 사용할지 고민하였습니다.
> - `customCell`을 만들어 등록하는 방식이 익숙하여 자주 사용했었는데, 이번에는 `default`로 지원되는 값들이 있고 구현되는 코드가 적은 `Modern cell configuration`방식을 사용하였습니다. 
> - 하지만 세세한 부분을 커스텀하기까지는 난이도가 있었고 자유도가 적어 사용하기 어려웠습니다. 러닝커브가 있어서 저희를 고생하게 만든 녀석입니다.

<br>

### 2. 통신해서 얻은 객체가 이미지 `url`을 가지고 있는데, 이러한 이미지를 다시 다운로드를 어느시점에 할것인지에 대하여 고민이 있었습니다. 
> - `init` 메서드를 변경하여 디코딩할때 이미지값도 같이 가져오도록 변경하였습니다.

```swift
init(from decoder: Decoder) throws {
        let container = try decoder.container(keyedBy: CodingKeys.self)
        id = try container.decode(Int.self, forKey: .id)
        vendorId = try container.decode(Int.self, forKey: .vendorId)
        name = try container.decode(String.self, forKey: .name)
        thumbnail = try container.decode(String.self, forKey: .thumbnail)
        currency = try container.decode(String.self, forKey: .currency)
        price = try container.decode(Double.self, forKey: .price)
        bargainPrice = try container.decode(Double.self, forKey: .bargainPrice)
        discountedPrice = try container.decode(Double.self, forKey: .discountedPrice)
        stock = try container.decode(Int.self, forKey: .stock)
        createdAt = try container.decode(String.self, forKey: .createdAt)
        issuedAt = try container.decode(String.self, forKey: .issuedAt)
        thumbnailImage = convertImage()
    }
```

<br>

### 3. 재사용되는 `Cell`로 인한 레이블의 `Color`변화를 초기화 하기 위해 고민하였습니다.
> - `GridCell`에서 의도치 않은 `textLabel`의 색상변화가 있었습니다. 할인이 있을때만 색깔이 빨간색이되어야 하는대 셀이 재사용되면서 빨간색이 남아있는 경우가 있었습니다.
> - `GridCollectionViewCell` 클래스 내부에 아래와 같이 초기화하는 코드를 작성해주어 해결하였습니다.

```swift
override func prepareForReuse() {
        super.prepareForReuse()
        productPrice.attributedText = nil
    }
```
---

<br>

## 오픈마켓 II [[STEP 1]](https://github.com/yagom-academy/ios-open-market/pull/162)
### 1. 추가한 사진을 삭제하는 방법 & 5개로 제한걸기
> - 이미지 뷰를 위로 올렸을때 삭제가 되도록 `swipeGesterRecognizer`를 사용하여 만들었습니다. 애니매이션을 사용하여 움직이게끔 하면 더 좋을꺼 같습니다. 
> - 이전에 `UIPanGesterRecognizer`를 사용했었을 땐 가로 `scrollView`가 잘 동작하지 않는 이슈가 있었습니다.
> - 이미지 뷰 삭제시 + 버튼이 나오도록 하는 부분을 `if-else`문으로 구현하였습니다. 
> - 이미지 5개를 추가하면 버튼이 사라지게 되는데, 이후에 이미지를 삭제하면 버튼이 나오지가 않는걸 알게 되어 옵저버 패턴으로 구현되어야 할거 같다는 고민을 하였습니다.

<br>

```swift
if addImageHorizontalStackView.arrangedSubviews.count == 6 {
    addImageButton.isHidden = true
} else {
    addImageButton.isHidden = false
}
```

<br>

## 🥄 삽질한점 🥄
### 1. 명세서와 example Code의 파라미터가 동일하지 않아서 삽질...🤬🤬
> - `POST`방식으로 통신했을때 명세서에서의 `Parameter`와 `example code`에서의 네이밍이 달라서 오류 찾느라 삽질을 하였습니다.

---

<br>

## [[STEP 2]](https://github.com/yagom-academy/ios-open-market/pull/171)
## 🥄 삽질한점 🥄
### 1. 스크롤 뷰에 관하여 - 지난주 스텝에서 스크롤이 안되는 문제의 연장
- 스크롤 뷰를 설정할때 스크롤이 되지 않는 상황, 그리고 스크롤은 되지만 안에 컨텐츠가 변하지 않는 상황이 있었습니다. 
- 문제의 원인은 `frame`과` bound`개념을 잘 알고 `Constraint`를 줘야했는데 그러지 못했던게 원인이였던거 같습니다.
- 일단 `ContentsLayoutGuide`와 관련된 값을 정확히 주지 못하여 보라색 느낌표가 떳었습니다.(모호한 스크롤뷰의 `ContentsSize`가 모호하다는 에러였습니다.) 
- 그래서 `ContentsLayoutGuide`와 관련된 `Constraint`를 줬는데 그제서야 스크롤뷰의 `ContentsSize`가 잡혔습니다. 
- 하지만 스크롤이 움직이지는 않았는대 원인은 `Frame(화면에 보이는 틀)`과 `Bound(내용물)` 사이즈를 같게 주어서 였습니다. 
- `frame`의 `Constraint`를 수정하자 그제서야 스크롤이 되었습니다. 
- 하지만 스크롤만 되고 안에 보이는 화면은 변하지 않았는데 원인은 안에 내용물들의 `Constraint`를 바로 상위뷰에 상대적으로 준게아니라 `Root View`에 주어서 스크롤을 해도 위치가 변하지 않는것이였습니다. 
- 이후 `Constraint`를 수정하자 정상적으로 스크롤이 되었습니다.

<br>
