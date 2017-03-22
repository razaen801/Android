# Android
This repository contains some custom Android stuff: widgets, classes etc.

<h1>Dynamic Dialog Fragment</h1>
<p>This is a fragment that will be shown as an Alert Dialog on large layouts and a Full Screen Fragment on smaller layouts.</p>

<h2>Usage</h2>

1. Create a layout that will contain the contents you want to display in the dialog, e.g <code>fragment_my_content.xml</code>.

2. Create a simple Fragment that will inflate this layout, e.g. <code>MyContentFragment.java</code>:
```java
public class MyContentFragment extends Fragment {
    @Nullable
    @Override
    public View onCreateView(LayoutInflater inflater, @Nullable ViewGroup container, @Nullable Bundle savedInstanceState) {
        return inflater.inflate(R.layout.fragment_my_content, container, false);
    }

```

2. Sublcass DynamicDialogFragment and implement the abstract methods
```java
public class MyDialogFragment extends DynamicDialogFragment {

    @Override
    protected void onPositiveButtonClick(View view) {
    
    }

    @Override
    protected void onNeutralButtonClick(View view) {

    }

    @Override
    protected void onNegativeButtonClick() {
    
    }
}
```

3. Create a NewInstance static method, like so: <br/>
```java
public static MyDialogFragment newInstance(/*your arguments if needed*/) {
    MyDialogFragment fragment = new MyDialogFragment();
    // Your specific code
    fragment.setRetainInstance(true);
    return fragment;
}
```

4. Set some properties by overriding the onCreate method:
```java
@Override
public void onCreate(@Nullable Bundle savedInstanceState) {
    super.onCreate(savedInstanceState);
    // This will load your actual content in the Dialog (default is an empty Fragment)
    mContentFragment = new MyContentFragment();
    Bundle args = getArguments();
    if (args != null) {
        // Set this to allow layout size detection (default is false: small layout)
        mIsLargeLayout = args.getBoolean("layout");
        // Set this to add a third button if needed (default is false: no third button)
        mIsLargeLayout = args.getBoolean("neutral");
    }
}
```

5. Create an instance of your class, like so (as referenced <a href="https://developer.android.com/guide/topics/ui/dialogs.html#FullscreenDialog">here</a> by the Android API):
```java
MyDialogFragment myDialogFragment = MyDialogFragment.newInstance();

// Set some configuration values for the dialog
Bundle args = new Bundle();
// Information on how to create the mIsLargeLayout property is in the link above
args.putBoolean("layout", mIsLargeLayout);
args.putString("button_positive", getString(R.string.your_positive_button_string));
args.putString("button_negative", getString(R.string.your_negative_button_string));
// This is needed only if you set a third button in step 4
args.putString("button_neutral", getString(R.string.your_neutral_button_string));
myDialogFragment.setArguments(args);

String title = getString(R.string.your_dialog_title_string);
FragmentManager fragmentManager = getSupportFragmentManager();
if (mIsLargeLayout)
    myDialogFragment.show(fragmentManager, title);
else {
    // The device is smaller, so show the fragment fullscreen
    FragmentTransaction transaction = fragmentManager.beginTransaction();
    // For a little polish, specify a transition animation
    transaction.setTransition(FragmentTransaction.TRANSIT_FRAGMENT_OPEN);
    // To make it fullscreen, use the 'content' root view as the container
    // for the fragment, which is always the root view for the activity
    transaction.add(android.R.id.content, myDialogFragment, title)
            .addToBackStack(null).commit();
}
```

