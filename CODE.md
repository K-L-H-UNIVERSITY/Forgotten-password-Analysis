import email
from django.shortcuts import render , redirect
from django.contrib.auth.models import User , auth
from django.contrib import messages
from .models import Students
# Create your views here.
def index(request):
    return render(request,'index.html')


def register(request):
    if request.method == 'POST':
        uname = request.POST['uname']
        email = request.POST['email']
        password = request.POST['password']
        cpassword = request.POST['cpassword']

        if password==cpassword:
             user = Students(username=uname,password=password)
             user.save()
             print("registered")
             messages.info(request,'Registration successfull..!')
             return redirect('register')
        else:
            print('passwords donot match :(')
            messages.info(request,'Passwords does not match')
            return redirect('register')
    else:
        return render(request , 'register.html')

def login(request):
    if request.method == 'POST':
        uname = request.POST['uname']
        password = request.POST['password']
        
        u = Students.objects.get(username__exact=uname)
        # if u:
        #     auth.login(request,user)
            
        passw = u.password
            # print('login success')
            # return render(request,'index.html',{'uname':uname,'passw':passw})
        # else:
            
        #     messages.info(request,'Invalid credentials')
        #     return redirect('login')
        print(u.password)
        if password == u.password:
            print('login success')
            return render(request,'index.html',{'uname':uname,'passw':passw})
        else:
            s_p_l=len(u.password)
            typed_password = password
            t_p_l = len(typed_password)

            
            dp = [[0 for x in range(t_p_l + 1)] for x in range(s_p_l + 1)]
            for i in range(s_p_l + 1):
                    for j in range(t_p_l + 1):
                        if i == 0:
                            dp[i][j] = j
                        elif j == 0:
                            dp[i][j] = i

                        elif u.password[i - 1] == typed_password[j - 1]:
                            dp[i][j] = dp[i - 1][j - 1]

                        else:
                            dp[i][j] = 1 + min(dp[i][j - 1],
                                            dp[i - 1][j],
                                            dp[i - 1][j - 1])

                    x=dp[s_p_l][t_p_l]
            difference = s_p_l - t_p_l
            if difference>4 :
                messages.info(request,'Entered password is completely invalid')
                return redirect('login')

            if x >= 1 and x <= 3:
                messages.info(request,f'your almost there there is a mismatch of only {x} letters in your passsword')
                return redirect('login')

            elif x >= 4 and x <= 6:
                messages.info(request,f"COME ON DONT GIVE UP THERE IS ONLY MISMATCH OF  {x} letters in your passsword")
                return redirect('login')

            elif x >= 7 and x <= 10:
                messages.info(request,f"SORRY THERE ARE {x} mismatching  letters in your password")
                return redirect('login')


            else:
                messages.info(request,f"SORRY TRY AGAIN YOUR PASSWORD DOSEN'T MATCHING WITH {x} letters in your passsword")
                return redirect('login')

                
    else:
        return render(request , 'login.html')
