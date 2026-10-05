#include <iostream>
#include <cmath>
#include <vector>
#include <string>
using namespace std;

// 三角形题目类（修改：增加题目编号、用户答案、正确答案）
class TriangleQuestion
{
private:
    int id;            //题目编号
    double a, b, c;    //三边
    double userAns;    //用户答案
    double rightAns;   //正确答案（这里用面积作为答案）
    bool isValid(double x, double y, double z)
    {
        return (x>0 && y>0 && z>0 && x+y>z && x+z>y && y+z>x);
    }
public:
    //构造函数 初始化
    TriangleQuestion(int id_=0, double a1=1, double b1=1, double c1=1)
    {
        id = id_;
        if(isValid(a1,b1,c1))
        {
            a = a1;
            b = b1;
            c = c1;
        }
        else
        {
            cout << "输入边长不能构成三角形，默认三边1,1,1" << endl;
            a = 1; b = 1; c = 1;
        }
        calcRightAnswer(); //自动计算正确答案
        userAns = 0;
    }

    //计算正确答案（面积）
    void calcRightAnswer()
    {
        double p = (a+b+c)/2.0;
        rightAns = sqrt(p*(p-a)*(p-b)*(p-c));
    }

    //用户输入自己的答案
    void setUserAnswer(double ans)
    {
        userAns = ans;
    }

    //获取正确答案
    double getRightAnswer()
    {
        return rightAns;
    }

    //获取用户答案
    double getUserAnswer()
    {
        return userAns;
    }

    //修改三边
    void setSide(double a1, double b1, double c1)
    {
        if(isValid(a1,b1,c1))
        {
            a = a1; b = b1; c = c1;
            calcRightAnswer();//改完边长重新计算标准答案
        }
        else
        {
            cout << "边长无效，不修改" << endl;
        }
    }

    //获取三边、题号
    int getId(){return id;}
    double getA(){return a;}
    double getB(){return b;}
    double getC(){return c;}

    //输出题目信息
    void showQuestion()
    {
        cout << "【题目" << id << "】三边："<<a<<" "<<b<<" "<<c<<endl;
        cout << "标准答案(面积)：" << rightAns << endl;
        cout << "你的答案：" << userAns << endl;
    }

    //求周长
    double getPerimeter()
    {
        return a+b+c;
    }
    //求面积
    double getArea()
    {
        double p=(a+b+c)/2;
        return sqrt(p*(p-a)*(p-b)*(p-c));
    }
    //判断三角形类型
    void judgeType()
    {
        if(a==b&&b==c) cout << "等边三角形";
        else if(a==b||b==c||a==c) cout << "等腰三角形";
        else if(a*a+b*b==c*c || a*a+c*c==b*b || b*b+c*c==a*a) cout << "直角三角形";
        else cout << "普通不等边三角形";
        cout << endl;
    }
};

// 题库类（整体类）
class QuestionBank
{
private:
    string bankName;                     //题库名称
    int questionCount;                   //题目数量
    vector<TriangleQuestion> quesList;   //保存所有题目
    vector<double> scoreList;            //每道题得分
    double avgScore;                     //平均分
public:
    //构造函数初始化题库
    QuestionBank(string name="三角形题库")
    {
        bankName = name;
        questionCount = 0;
        avgScore = 0;
    }

    //增加题目到题库
    void addQuestion(TriangleQuestion q)
    {
        quesList.push_back(q);
        questionCount++;
    }

    //按题号删除题目
    void delQuestion(int id)
    {
        for(auto it = quesList.begin(); it != quesList.end(); it++)
        {
            if(it->getId() == id)
            {
                quesList.erase(it);
                questionCount--;
                cout << "成功删除题目" << id << endl;
                return;
            }
        }
        cout << "没有找到该题目" << endl;
    }

    //查询题目，按题号显示
    void searchQuestion(int id)
    {
        for(auto &q : quesList)
        {
            if(q.getId() == id)
            {
                q.showQuestion();
                return;
            }
        }
        cout << "未找到题号为" << id << "的题目"<<endl;
    }

    //录入本题得分，并且重新计算平均分
    void addScore(double s)
    {
        scoreList.push_back(s);
        double sum = 0;
        for(auto sc : scoreList) sum += sc;
        avgScore = sum / scoreList.size();
    }

    //输出题库信息
    void showBankInfo()
    {
        cout << "\n=======《"<<bankName<<"》======="<<endl;
        cout << "题目总数：" << questionCount << endl;
        cout << "已做题平均分：" << avgScore << endl;
        cout << "全部题目列表："<<endl;
        for(auto &q : quesList)
        {
            q.showQuestion();
        }
    }

    //修改题库名称
    void setBankName(string name)
    {
        bankName = name;
    }
    string getBankName()
    {
        return bankName;
    }
};

//主函数测试
int main()
{
    //1.创建题库
    QuestionBank bank("三角形几何题库");

    //2.创建2道三角形题目，加入题库
    TriangleQuestion q1(1,3,4,5);
    TriangleQuestion q2(2,2,2,2);
    bank.addQuestion(q1);
    bank.addQuestion(q2);

    //3.用户做第1题，输入答案
    q1.setUserAnswer(6);
    bank.addScore(10); //满分10分

    //4.用户做第2题
    q2.setUserAnswer(1.732);
    bank.addScore(10);

    //5.查询题号1
    cout << "查询题号1："<<endl;
    bank.searchQuestion(1);

    //6.显示整个题库信息
    bank.showBankInfo();

    //7.删除题号2
    bank.delQuestion(2);
    bank.showBankInfo();

    return 0;
}
