title: Android平台上基于RxJava的异步编程
---
作为现今的应用开发者，面临着两个相当不方便的现状：
1.应用程序多数操作需要异步操作
2.正确的处理异步操作并不容易

当前Android处理异步方式：
1.doInBackground处理延时操作
2.onPostExecute处理UI操作

    class DownloadTask extends AsyncTask<String, Void, File> {

      protected File doInBackground(String... args) {
        final String url = args[0];
        try {
          byte[] fileContent = downloadFile(url);
          File file = writeToFile(fileContent);
          return file;
        } catch (Exception e) {
          // ???
        }
      }

      protected void onPostExecute(File file) {
        Context context = getContext(); // ???
        Toast.makeText(context,
            "Downloaded: " + file.getAbsolutePath(),
            Toast.LENGTH_SHORT)
            .show();
      }
    }

<!-- more -->

看起来一切正常。但是仔细思考，并不是万事如意，要处理的事情远比上例的情况要复杂。假设downloadFile操作过程中发生异常，也许你需要try-catch或者在downloadFile函数返回一个空的数组并进行判断，如果，你想要告诉用户发生了什么错误，那么便需要通知UI线程更新界面，Toast或者是改变某个控件，但是你无法再doInBackground中直接操作这些，因为只有UI层可以更改界面元素。

到这里，第一反应是认为onPostExecute中处理错误并不复杂。也许你会定义一个变量来告诉onPostExecute是发生了何种错误，或者定义一个局部变量来传递应该显示何种信息给用户。但是这种情况下，我们必须在我们的异步任务中持有Context的引用，但假设我们的doInBackground操作持续了数分钟，那么便极易引发内存泄露，因为程序中该上下文Activity可能已经从界面中结束了，而且即使在Activity的onDestroy中Cancel了该AsyncTask也无法避免此种泄露。

更复杂的情况：
上述的下载任务只是一个非常简单的下载，假设说此时有更加复杂的耗时操作，比如从服务器调用API接口，API接口返回了Json，需要解析Json，转换结果，过滤结果或者是储存结果，此时可能需要对这些操作进行组合，也许某些时候他们只是需要过滤结果，也许某些时候是储存结果，而这些组合又不得不将他们封装成一个个任务class，或者是再一个class传入各种各样的参数来进行组合，又或者将所有的AsyncTask单独定义成类，在完成一个后启动另一个，完成一个后启动另一个，不断的交叉调用。
显然，这种方式显得臃肿而且易出错。再者，AsyncTask并不是一个如一而终的类，随着API版本的升高，他也在不断的完善，但是这也代表着它在某些API等级上存在问题。

### 新的解决方案：函数响应式编程 ###

RxJava是一个基于JVM的响应式编程库。函数响应式编程基于观察者模型。并发操作被看成是观察者，执行时注入观察者，每个观察者实现三个处理函数，onNext(),onError(),onComplete()，并且可以指定观察者与被观察者分别在何种线程中执行。
上述下载文件的代码用RxJava实现后如下：

    public static Observable<File> downloadFileObservable(){
        return Observable.create(new Observable.OnSubscribe<File>() {
            @Override
            public void call(Subscriber<? super File> subscriber) {
                try {
                    byte[] fileContent = downloadFile();
                    File file = writeToFile(fileContent);
                    fileObserver.onNext(file);
                    fileObserver.onCompleted();
                } catch (Exception e) {
                    fileObserver.onError(e);
                }
            }
         });
    }

此处生成了一个耗时操作的被观察者等待注入，当下载并写入完成后调用onNext与OnComplete通知观察者，并在发生异常的情况下将异常传递给观察者的onError执行。
RxJava的支持库RxJava-Android提供了另外的衍生方法，他支持绑定Activity与Fragment，保证再Activity与Fragment销毁的情况下不调用到观察者，并且提供了一个主线程Scheduler。

    class MyFragment extends Fragment implements Observer<File> {
      private Subscription subscription;

      @Override
      protected void onCreate(Bundle savedInstanceState) {
        subscription = AndroidObservables.bindFragment(this, downloadFileObservable())
                              .subscribeOn(Schedulers.newThread())
                              .subscribe(this);
      }

      private Observable<File> downloadFileObservable() { /* as above */ }

      @Override
      protected void onDestroy() {
        subscription.unsubscribe();
      }

      public void onNext(File file) {
        Toast.makeText(getActivity(),
            "Downloaded: " + file.getAbsolutePath(),
            Toast.LENGTH_SHORT)
            .show();
      }

      public void onCompleted() {}

      public void onError(Throwable error) {
        Toast.makeText(getActivity(),
            "Download failed: " + error.getMessage(),
            Toast.LENGTH_SHORT)
            .show();
      }
    }

如上所述，虽然bindFragment会保证只在Fragment有效的情况下执行到onXXX方法，但是依然需要在onDestroy中调用subscription.unsubscribe方法保证资源的正确回收避免内存泄露。
再来对比此种机制与AsyncTask。
1.错误处理方面不再存在需要定义各种整形变量与局部变量的情况，错误在子线程中组织完成后传递给onError方法。
2.不用关心线程的调度，调用subscribeOn与observeOn可方便设置两条流分别所处的线程。
3.不用担心内存的泄露。
4.组合起来更加的方便，如：
当某天另外有处需求变更为需要将下载后的文件内容显示出来给用户时，只需再原有的代码上增加一个

    Observable<String> filePathObservable = downloadFileObservable().map(new Func1<File, String>() {
        @Override
        public String call(File file) {
            fileContent = readFileContent();
            return fileContent;
        }
    });

便可将原来返回下载文件的操作变更为返回文件内容，并轻松注入观察者进行显示。代码的扩展性得到了保证。
5.单元测试更加方便。因为可以不依赖于Android运行，可以直接对某个耗时操作进行单元测试
