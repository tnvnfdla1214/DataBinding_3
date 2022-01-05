# 리사이클러 뷰에 databinding적용해보기


## 그래들 추가
 ```Kotlin
android {
    ...
    dataBinding {
        enabled = true
    }
}
```

## layout
리사이클러의 아이템뷰 의 xml을 \<layout>으로 감싼다.

![image](https://user-images.githubusercontent.com/48902047/148255565-09b5b259-1923-4bc0-9680-10af1d1d6d7a.png)

view와 data를 @{}로 bind해준다.
 ```Kotlin
<?xml version="1.0" encoding="utf-8"?>
<layout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools">

    <data>

        <variable
            name="profileRecycler"
            type="changhwan.experiment.sopthomework.FollowerData" />
    </data>

    <androidx.constraintlayout.widget.ConstraintLayout
        android:id="@+id/followerLayout"
        android:layout_width="match_parent"
        android:layout_height="wrap_content">

        <ImageView
            android:id="@+id/imageView"
            android:layout_width="49dp"
            android:layout_height="0dp"
            android:layout_marginLeft="21dp"
            android:layout_marginTop="24dp"
            android:layout_marginBottom="24dp"
            app:layout_constraintBottom_toBottomOf="parent"
            app:layout_constraintDimensionRatio="1:1"
            app:layout_constraintStart_toStartOf="parent"
            app:layout_constraintTop_toTopOf="parent"
            app:recyclerGlide="@{profileRecycler.followerImg}" />

        <TextView
            android:id="@+id/followerName"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:layout_marginLeft="22dp"
            android:layout_marginTop="25dp"
            android:fontFamily="@font/noto_sans_kr"
            android:includeFontPadding="false"
            android:text="@{profileRecycler.followerName}"
            android:textFontWeight="700"
            android:textSize="16sp"
            android:textStyle="normal"
            app:layout_constraintStart_toEndOf="@+id/imageView"
            app:layout_constraintTop_toTopOf="parent"
            tools:text="이름" />

        <TextView
            android:id="@+id/followerIntro"
            android:layout_width="150dp"
            android:layout_height="wrap_content"
            android:layout_marginTop="5dp"
            android:layout_marginBottom="24sp"
            android:ellipsize="end"
            android:fontFamily="@font/noto_sans_kr"
            android:includeFontPadding="false"
            android:maxLines="1"
            android:text="@{profileRecycler.followerIntro}"
            android:textFontWeight="400"
            android:textSize="14sp"
            android:textStyle="normal"
            app:layout_constraintBottom_toBottomOf="parent"
            app:layout_constraintStart_toStartOf="@+id/followerName"
            app:layout_constraintTop_toBottomOf="@+id/followerName"
            tools:text="자기소개" />
    </androidx.constraintlayout.widget.ConstraintLayout>
</layout>
```
## viewholder 클래스에서 binding객체에 데이터 담아준다.
onbind 함수에서 일일히 다 넣었던거 그냥 itemview에서 설정해놓은 데이터 변수에 매번 들어오는 data를 넣어주는거로 바꿔준다.
 ```Kotlin
 inner class FollowerViewHolder(
        private val binding: FollowerItemBinding,
        listener: ItemDragListener
    ) : RecyclerView.ViewHolder(binding.root) {
        fun onBind(data: FollowerData) {
            binding.profileRecycler = data
            // binding.executePendingBindings() -> 없어도 된단다 이거 바인딩할때 작업들 당장당장 수행하라고 강요하는 함수. 
        }
        //중략
 }
```
## 이미지 같은거 처리를위해 bindingadpter 만들어주기
 ```Kotlin
package changhwan.experiment.sopthomework


import android.graphics.drawable.Drawable
import android.widget.ImageView
import androidx.databinding.BindingAdapter
import androidx.lifecycle.MutableLiveData
import com.bumptech.glide.Glide


object BindingAdapters {

    @JvmStatic
    @BindingAdapter("recyclerGlide")
    fun setImage (imageview : ImageView, url : MutableLiveData<String>){
        Glide.with(imageview.context)
            .load(url.value)
            .circleCrop()
            .into(imageview)
    }
}
```
기존에 bindingadapter 공부했던것처럼 처리 불가능한거 만들어준다. -> 이미지처리 이제 glide 추가했기에 그걸로 처리했다.

그래서 이미지뷰에서 이미지 넣는부분이 이렇다
 ```Kotlin
app:recyclerGlide="@{profileRecycler.followerImg}"
```
결론적으로 layout 감싸주고 데이터 만들어주는곳은 item이고

viewholder에서 데이터 집어넣어주는형태이다
