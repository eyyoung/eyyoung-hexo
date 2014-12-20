title: 自定义控件保存状态
---

处理自定义控件的onSaveInstance
<!-- more -->
方法一：

    public class CustomView extends View {

      private int stateToSave;

      ...

      @Override
      public Parcelable onSaveInstanceState() {
        //begin boilerplate code that allows parent classes to save state
        Parcelable superState = super.onSaveInstanceState();

        SavedState ss = new SavedState(superState);
        //end

        ss.stateToSave = this.stateToSave;

        return ss;
      }

      @Override
      public void onRestoreInstanceState(Parcelable state) {
        //begin boilerplate code so parent classes can restore state
        if(!(state instanceof SavedState)) {
          super.onRestoreInstanceState(state);
          return;
        }

        SavedState ss = (SavedState)state;
        super.onRestoreInstanceState(ss.getSuperState());
        //end

        this.stateToSave = ss.stateToSave;
      }

      static class SavedState extends BaseSavedState {
        int stateToSave;

        SavedState(Parcelable superState) {
          super(superState);
        }

        private SavedState(Parcel in) {
          super(in);
          this.stateToSave = in.readInt();
        }

        @Override
        public void writeToParcel(Parcel out, int flags) {
          super.writeToParcel(out, flags);
          out.writeInt(this.stateToSave);
        }

        //required field that makes Parcelables from a Parcel
        public static final Parcelable.Creator&lt;SavedState&gt; CREATOR =
            new Parcelable.Creator&lt;SavedState&gt;() {
              public SavedState createFromParcel(Parcel in) {
                return new SavedState(in);
              }
              public SavedState[] newArray(int size) {
                return new SavedState[size];
              }
        };
      }
    }

方法二：

    public class CustomView extends View {

      private int stateToSave;
      // ... variables

      @Override
      public Parcelable onSaveInstanceState() {

        Bundle bundle = new Bundle();
        bundle.putParcelable("instanceState", super.onSaveInstanceState());
        bundle.putInt("stateToSave", this.stateToSave);
        // ... save everything
        return bundle;
      }

      @Override
      public void onRestoreInstanceState(Parcelable state) {

        if (state instanceof Bundle) {
          Bundle bundle = (Bundle) state;
          this.stateToSave = bundle.getInt("stateToSave");
          // ... load everything
          state = bundle.getParcelable("instanceState");
        }
        super.onRestoreInstanceState(state);
      }
    }
