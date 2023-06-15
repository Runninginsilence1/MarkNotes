��һ����Spigot ��������
1. �������غ���spigot�ĺ���jar�ļ�

2. ȷ������·���в�������ASCII�루����ŵ�������Ŀ¼�����ļ����ڡ����ļ�����Ϊ����Ĺ���Ŀ¼��

3. �����������ļ���txt�ļ��޸�Ϊbat�ļ�����ͨ�������������к��ĵ�java����
��������
```cmd
@echo off
java -Xmx1g -Xms1g -jar spigot-1.19.jar
pause
```
�򵥵�˵����һ���ı��ļ����Ѻ�׺���ĳ�bat�����е�������������롣����������������������������������Ϳ���ִ����

4. ��һ������
��һ������ʱ��־����ʾ����Ҫͬ��elua.txt�ļ��е�������Ȼ�����ǿ���˳���
���žͻ����һ��eula.txt�ļ����ļ����ݸĳ�eula=true

5. �ڶ�������
�ڶ�������ʱ����̨���ӡ��������־��˵������������С��ȴ�����ֹ̨ͣ���֮��ͨ������̨��ӡ�����һ���������ݷ�������Ǹ���������help���Բ鿴���������

��ؼ�סÿ��ʹ��stop�����������̨���رշ�������������ܳ��ַ������ص�������

6. �޸�server.properties�ļ�
���Ƿ������������ļ��������������������޸�online-model��ѡ��Ϊfalse��online-mode=false

7. �޸���ɺ��ٴ�����bat�ļ��͵��ڷ������Ѿ�������

�ڶ�����ʹ��Sakura frp����������͸
1. ����sakura frp���
2. ������Կ����¼
3. 


���ڸ�Spigot(ˮ��ͷ��)��ӷ�����ģ�������
��������Ҫ����������jar�ļ���
forge��18.2�İ汾�����

spigot �� forge �ǲ�ͬ�ķ��������ģ�ǰ��֧��ԭ������������ǲ�֧��mod��Ҳ������ǰ�����õ��Ǹ�(���¶�ʮ��) 

���¶�ʮ�� ���ʣ�
earnings нˮ������
garden ��԰����԰
dental ���ݵģ����Ƶģ�
predictable ��Ԥ�Եģ�����֮�еģ�
sustainable �ɳ����ģ��ɳ������õģ�
blame ��֣�
confusion ���󣻻��Ҿ��棻
sustainability �ɳ����ԣ��;���
genuine �����ģ���ϵģ�
thorough (ע������ though) ���׵ģ���ȫ�ģ�
acting
afraid ���µģ����ĵ�
household һ���ˣ�����������
afraid ���µģ�
poet ʫ�ˣ�
comment ���ۣ�ע��
profession ְҵ����ҵ
steep ���͵ģ����ߵģ������
appearance ��ò�����֣���Դ��
fame n������������
fortune �Ƹ������Ǯ������
quantity ����������
farm ũ��
bicycle ���г�
municipal ������
retire ���ݣ����ޣ��뿪��
peaceful ��ƽ�ģ�
retire
steep
peaceful
municipal

### ���¶�ʮ�Żع�Fragment������
Fragment
9.0�汾�� ʹ��Fragment��ע����Ҫ�̳�androidx�������Fragment����Ϊ��ͨ��׿�������Fragment�ڰ�׿9.0�����Ѿ�����
Ϊ�˱��ֶԸ߰汾�ļ�����

��̬���ز�����Ҫʹ��LayoutInflater���ʵ�������Է�����

ʵս��Activity���涯̬���Fragment
(1) ���������Fragment ��ʵ����
(2) ��ȡFragmentManager����Activity �п���ֱ�ӵ���getSupportFragmentManager()
������ȡ��
(3) ����һ������ͨ������beginTransaction()����������
(4) ����������ӻ��滻Fragment ��һ��ʹ��replace()����ʵ�֣���Ҫ����������id�ʹ���
�ӵ�Fragment ʵ����
(5) �ύ���񣬵���commit()��������ɡ�

```java
import androidx.appcompat.app.AppCompatActivity
import android.os.Bundle
import android.widget.Button
import androidx.fragment.app.Fragment

class MainActivity : AppCompatActivity() {
    private lateinit var button: Button
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_main)
        button = findViewById(R.id.button)
        button.setOnClickListener {
            replaceFragment(AnotherRightFragment())
        }
        replaceFragment(RightFragment())
    }

    private fun replaceFragment(fragment: Fragment) {
        val fragmentManager = supportFragmentManager
        val transaction = fragmentManager.beginTransaction()
        transaction.replace(R.id.rightLayout, fragment)
        transaction.commit()
    }
}
```


#### ����Fragment�ķ���ջ��
����ʹ����һϵ�в���ͨ�����붯̬�滻����ص�Fragment
��ʱ��Ϊ��ֱ���滻�Ĺ�ϵ�����ֱ��ʹ��back�����˵Ļ���ֱ���˳�����
���仰˵FragmentĬ����û������Activity������ջ�ġ�
����ͨ�������ֶ���ӡ�
�벻Ҫ���Ƕ�Fragment�Ĳ�����ͨ��һ������ʵ�ֵġ�
����������Ҳ�ṩ��һ��addToBackStack()�ķ�����

��֮�ǵ��������������һ������Ϳ���ʵ��Fragment�ķ���ջ�����ˡ�



#### Fragment��Activity�Ľ���
��ȻFǶ����Activity���棬���ǴӴ���Ĳ�����˵�����������������࣬�˴��ǲ���ֱ�ӵ��õ�

��Ҫʵ�������ᵽ�Ĺ��ܣ�
1. ͨ��FragmentManager��ȡ��Fragment��ʵ����ʵ�ִ�A��F

2. ��F�п���ͨ��getActivity��������ȡA��ʵ����

3. ��ͬ��Fragment֮����н����ķ�ʽ�ƺ��е㻬����ͨ������ķ�ʽʵ��һ�� F �� A �� F�ķ�ʽ������

#### Fragment����������
F������������A�Ĵ�����ͬ����������˼������ӵ��������ڣ�ֱ�Ӵ�pdf���и���
onAttach()����Fragment ��Activity ��������ʱ���á�
onCreateView()��ΪFragment ������ͼ�����ز��֣�ʱ���á�
onActivityCreated()��ȷ����Fragment �������Activity �Ѿ��������ʱ���á�
onDestroyView()������Fragment ��������ͼ���Ƴ�ʱ���á�
onDetach()����Fragment ��Activity �������ʱ���á�


#### ����Fragment��ʵ������
����Fragment�ǿ���ֱ��ʵ�����ģ������ڰ�׿�����ǿ�����ô���ġ�
��ΪF�������� Activity�Ĳ����ļ��������� ���FragmentManager�ṩ��һ��findFragmentById������ֱ�ӻ��ʵ��������

### Android�е�SQLite���ݿ�
���ӵĹ�ϵ�����������ݿ���ʵ��


## ����forge(����������mod�ķ�����)
### ����
1. ����forge
2. ����Java
3. ��forge��������װ�����
4. ���������̷����

### ���ڷ���������
����˼�壺���������ľ��ǿ����Ĺ��ߣ�û������û�з�������
�����棨��ԭ��ķ������� ��Щ����˶�װ����mod
����ʹ��forge����װmod
Ϊ����Ӧ�ҵĿͻ�����Ӧ�ð�װ��forge�İ汾Ӧ����1.18.2

