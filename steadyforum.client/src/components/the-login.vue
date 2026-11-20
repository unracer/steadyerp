<!--we not garanted more 50% uptime

    + персональный цвет загрузки с краю от логина (open source антифишинг)

    уникалльная возможность блокировать вход после потери фокуса, если вход был с недоверенного устройства
    *отдельный модуль по странному поведению пользователя, по ключевым словам поиска

    -->

<template>
    <div class="loginWrapper" v-if="isloginshow">
        <p> we are next generation of society network.</p>
        <p> chat anon and fast with us.</p>
        <p> we do not guaranted more 50% uptime</p>
        <p> but still freedom place</p>

        <div class="loader" >
            <div class="inner one" style="border-bottom: 3px solid {{loadercolor}};"></div>
            <div class="inner two" style="border-right: 3px solid {{loadercolor}}; "></div>
            <div class="inner three" style="border-top: 3px solid {{loadercolor}};"></div> 
        </div>

        <br>
        <input id="loginName" v-model="userLoginInput" /><br />
        <!--<input id="loginPassword" v-model="userPasswordInput" type="password" /><br />-->
        <button id="loginBtn" v-on:click="() => userLogin()">LOGIN</button>
        <div v-if="loginMessage != null" class="error">{{loginMessage}}</div>
    </div>
</template>

<script lang="ts">
    import { defineComponent } from 'vue';

    export default defineComponent({
        data(): Data {
            return {
                loading: false,
                post: null,
                isloginshow: true,
                userLoginInput: "aezakmi",
                userPasswordInput: null,
                issessionexpired: true,
                loginMessage: null,
                loadercolor: "#ffff00",
            };
        },
        created() {
            // fetch the data when the view is created and the data is
            // already being observed
            //this.requestloadercolor();
            /*this.userLogin();*/
            if (this.$cookies.get("steadyforumname")) {
                this.userLoginInput = this.$cookies.get("steadyforumname");
            }
        },
        watch: {
            // call again the method if the route changes
        },
        methods: {
            async sha256(message) {
                const msgBuffer = new TextEncoder('utf-8').encode(message);
                const hashBuffer = await crypto.subtle.digest('SHA-256', msgBuffer);
                const hashArray = Array.from(new Uint8Array(hashBuffer));
                const hashHex = hashArray.map(b => ('00' + b.toString(16)).slice(-2)).join('');
                return hashHex;
            },
            async userLogin() {  
                this.$cookies.remove('steadyforumname');
                this.$cookies.remove('steadyforumavatar');
                this.$cookies.set("steadyforumname", this.userLoginInput, "expiring time");
                localStorage.setItem('user_avatar', "data:image/jpeg;base64,/9j/4AAQSkZJRgABAQEASABIAAD/2wBDAAYEBQYFBAYGBQYHBwYIChAKCgkJChQODwwQFxQYGBcUFhYaHSUfGhsjHBYWICwgIyYnKSopGR8tMC0oMCUoKSj/2wBDAQcHBwoIChMKChMoGhYaKCgoKCgoKCgoKCgoKCgoKCgoKCgoKCgoKCgoKCgoKCgoKCgoKCgoKCgoKCgoKCgoKCj/wgARCAEzAUADASIAAhEBAxEB/8QAHAAAAgIDAQEAAAAAAAAAAAAAAAECBAMFBgcI/8QAGAEBAQEBAQAAAAAAAAAAAAAAAAECAwT/2gAMAwEAAhADEAAAAfKk0AAAAAAAAAAAAAwHMxGWBEaAAAAAAAAAAAAGmCaAAAAAAAABrf8Afr5d1XqeSXitzuhqrnmRDDZDTabsiPK+W99hZ87nsPn9zzo1YAAAAAAA0wTQAAAAAM6Y03p3TXZpDGkMEMiJNREmECaETRFTZxvmH0BTs+e31PLawgAAAAGmCaAAAAZ2hL1hk2OralBuoNoBsiSIi2CYAmWpSIiSDF5Z6wk+bjueG1hAIAA0wQAAMLJuPbNJv5uJOtLznS+XdxZ0A9euLZeY+hF4IkiKJsRIABghsiSURU0uHxX2/Wp8/K7T1zQANMQAAD9S4H3leb6DzztJvb0r1BPFuv4votc/XeH7fxia1/Y+f39Y9b03GUc79L2XlmwmvUMvEdFnW9VB2XihiNu6VisqGJSURjNLwfk/0h4PcaYC5GmCaAMx6f3uC1N+T721x1nsGj2Wrl8b7bh/VunHe+Hej+Zyyvw3tmgrZLrOtu66S7fbczjmu0uefGdehU+ME9L23nW1mvSrHDbmb6VVrQoyUseE7ytZ85mevrmNME0PqeW9OPSoSJvW+Pe3+VXPa6/S1Dj/AHDw/wBj1jz/AJ06su6zZ8bneK5r8++NJ5HbBNIhtRjSViqRuNxyVnO/Ueg8u6bHXuilemoxnE8Y5D1bynXMadgmh+n+Yd0vp17zrr5rb+b+jeWanGjhrgXagYu14y21a1MohZldTBV6Ck1pZSzXGHPtLU3pIbXJGkx7TanNx6vmlw7zndknofY+Q+m8u+1TZzXhf0R876wmnciaJe2eKfRS+YZ+75xrbeXRoa5zxZMeuciAEscws49pLk2uqU6bzU46Riya2F57ulSiti3WqG06zhtxnXUcVb16vda/tDkux5Xa46en2NZs83B84fSfzbrGJp6yJol9H/N/0KbWneU35pwHufiO+eLHOOuUCSI5IBnyUYtbF65rZrEUBpZuLSxVtxsqyUs3Lkw5LnN3vn+5z1v1tlW59u76ThuzyPm76G+dtYTT1kTQe4eH+oHpqam63hHs3i2sVWn04pScmOEsStDaQ0SniCaciDETSnZCWRxCZNHkcpqW/wBF1HPtf7rl+rx05/wz1Ly3XJNPUE0HT8zM+mcFKM3oPKul5XeHLFk3xbUIjiz4Fbi2pzxZLkjlsmvNzGa1y2HQy6Tb763z683V6nHN8NU6Tnt8MlgsmLbaC5L2Pacju+fbyzk82HfIadgmgAPTun8Z9ImuL01+h05mTDluJQQZqt2mqAUZkRW8WVN3Ye7z202xyQx0vla3NYR80mqo4sfXzbi7obqbzV9rxXPt2+qxcdNa9BrmNA00AA+i5yZutfZr7xiY7lAzYVbsJdWSjbLNXkl/JQyJvd9yO25+jfV8NfO9vHWZSxzG01Wueqx2V14vaY+857ucP2fHcu1XlcmLpzAEAYJoAAAMlqlk1nK29YhN5CzmxWJrT482KyJkCDQmbZafJNdpb5np+Xo1VDsReHj2ELnkr+3uljfc3g57fn9nU75iDWAAABpoAAAAAy2aU7m3mxWdZyW8WVrUK3jucWO0koliK4TKh26sZd9f5GWddvY4GU11uqp2ZbujqVAQXIAAAAwTQAAAAAAEr2vDpsnObTTYU71WyOOeNjHjywqImQxzjIptkXhrzVqmjOgBQAAAABpgmgAAAAAAAAACViqF+WudmwnrCthGiFiOEjLjSACUAAAAAAAAGmNAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAB/8QALhAAAQQBAgUEAgICAwEAAAAAAQACAwQRBRIGEBMgISIwMUAUMiNBMzQVJFBg/9oACAEBAAEFAv8A2sLY5bHLH/gwVprBr8PWHqHh+qxR6ZTjTYImrAWAjBE5SabTkU2gVXqxw9O1T1Z65+1R0uzcVPQq0KYxrG+05oeLmh1p1e0mzU+vVrS2pNN0OKv9HUtEhsq3UmqSfT0rSpbzqlWKrF9KzXisx6tpMlM/R0TRzZLGhjPqOaHDW9HMH0NB0o2ngABb/X9QjK1/Svx3e7o2nm/YjY2NiKim3WmOzzdYHV+i9oe3WtPNGx7deJ082nwNpRcpjtjZZDJqMwk5XpuhX06xvsslz2Z8oe5fqst17UD60/tcMUenC6b/ALMTshW/9eQ/y6FN/KFxHPthgm6TqN7eZrjYW/8AJZMeoZTLAcjL6WHsB9niej1YPZ02sbdzaI4rU2y7QkDmK67bVecv0Pzf+BrU/WuKu/Y6Wcvd1imTpk5aoLe4RT5XWGHyeOr6K8voEngO73NDm6rVNO77HCVXZDJ5brLC2xo8uWN8jWjigVwvHun1CYV68rt7wmswxx8AEoHCZIt5aYbhUmo4c68Oj+cPx23/AOBlvzFay1kngOz3cVVepW742GSSrCK9VavBvj0ubpz1nZbrnigVwxHtp8UzYaqkRlkvRhrHJrcRH5aVu8B2EXLeVv8ADX+WSDoQS7U23661nLopd47LEQmgnjMUvdw3B19Udyss3MtNMFvSpd8PEsmKa0lnTocQT9a/G0vdBD+PDdlBH9l38PsZQkKbYUU+5UplDNu7eJoOlqXdwezDeTh44ji2z6HZAj4hsdR7f2dabFQldvfoVcPF6TapnZcm/rjz24Q7GOwoLeI6VjYIZNwR5cXQ5r92gvEVOOXwDnlxQPSxzmF7i8p1mR0S0ucQ0bU293JoUcfqeFhALZ6mxep0JLCzCMfpDcosIJGE12DBJ506bc5hyOXEMfU0ruDXwVql/KrzZGcjiV+6xjsKjfhryh5QiKij8uiwZ2oJrMqGMETNDDE7y5ocj4NeAFTwYU4wQmfFWTa+pJuZy1Bm+j2hGuHVLFEtNG4WH8trY7s/5FnvYMKF6YQv6l8GQDMUgCfL4fOXIPwWWMOe7dJVOIbLsNnOXRNy5kXp+Dp9ggRHLVMMxO8O7G/tE3MEkIV2kHttCSMhHllDmz5YWLdhArrFqmm3DcsrcvlRwOkjTXYMdvAfZLkfJq/5THmGYbXafLtdXfnlJ/jk/fsHzTO6qQpIvGtVMt7gg7C6wXWXVKdISiebRlNX9cggeTTtdTfvhnjLntYWHTHbowpziF3l3bpDt+mcr0W6KbxJ2nmPYjblS45Dso2OkOp1A9u1abIdkRJGpP6dDu4Xk36Vym/S3/sdp78eyEF8CCUh79xGn+hQuy3iSXp6T3cGzcirUmGWTun7HewCvnsAW1beQWE4egHrSt9MVA5DQuL5cRd3D0/Q1NPdganOAyX9ux3cQtpWEFnK2EmvTc8R1A1PrDL6ikgLVhBNUb2tTJ45GU3EGI5bxJP1tT7mOLHadP8Ak07R8ajNl57CnfHYEE0oOCMLHr8Y7oKrWiP0oBpRb5LVdO0D5aPEbMumG11EDfAxilsCGrI8yP7+E7fpvy7GXHZchz/uTuBUeCoosg1QVFFsdlNAc1zCB1PM0gEdqXeQ5NnG2tMOpqMOHQ+HVZARxFa2wexSsOrWb8wmgn+eWeQ+XjIPYGrYmR5NSIhBpw5iGEG4TH+bAAVub+N/JqhHqsRbqGPNQ7FdnNiz7NGzmKb5PYE0eJW45hBAcoJHNUbzjcjIMxuwHHI6hIuZzyaq8bnWbvpqj9tSm2R+0Dg9TeHdg+Y/iYdmVuQcmSKKTK+QQWn8gBdXIkf4edyLfIYq0DnPoVNj9QedsszYmvcXu9trsEnPYPmJS/q7uBUMgULsotypK2U6N7F6uQZlRQ5NSDC3bRel2m1MZpPdacdgCaiMtd89zThVpcKJ7SgAnRhy/Fbl1MIVHZ6TYWtuMCltb23bPWd77TjmEz5KePUAsIjtBwmTlqiunP5mC28Eb7GMdqvmSWSZ0bNguWeqfotdhNOU0JqzlStQC2ohELCwsdmVlDJUcWV+SIBNO+X6gOFFMm/GMp7fGORCxywsdwOE+Yn7DJHMUNoIOa4O+VuWUVlE8wF8Jz0Tn7YOEJnrr5HUbjcEHLwshFy3reVn/wCf/8QAIhEAAgICAgIDAQEAAAAAAAAAAAECERAgEjATIQMxQEFR/9oACAEDAQE/Af1UUVmjiV2JdNdVdj3rteyyn7GxSOYp9D1Q8L7JsUf7lSPIcxS6mX6IlWx+lW6kJ5eVhl4TJOxRKxxKFGyUaIixLKxJ6IQ3i8QZOVnxqz6xLKxPs+KVZllYlldKPjeHrPK1UbFA4omqEiPp4eiJ7xiJVi6JOyLH/o362lsiLxZN4hAY9mt16Iuxws8ZwPSJS6GisPS6PIzyHMb7HtX4KKK/b//EAB4RAAIBBQEBAQAAAAAAAAAAAAABEQIQEiAwQFAx/9oACAECAQE/AffJJPvfyYtBiY2knslpBiYkdHxjRaq8CW0kjutVyaKUVMdlqulSvTrT1qstaeEjqMil2e63bJt+iQxEbLd3StVULdPgyTMzslwneDAwMfhSSST7f//EADcQAAECBAMGBAUDAwUAAAAAAAEAAgMQESEgIjESMEBBUWEEIzJxUFJigZETQqEUM4JDYHKiwf/aAAgBAQAGPwL436T+F6T+PgVIMNzvZec5sMfleY57/wCFl8Oz73WWEwfZaBaBZobD9leAz7LIXsXkvbEH4XnQ3N4urG7LPmcqxfNd30VGANHbd0cAR3VYflP7aKpbts+ZvD7EFu0UH+I8yJ/A4Eug+XE/grYjNp/7wm0ckH5uq2ILaDgyyM3aCL2Z4PXpwQjeIFIPIfMg1oo0aDhaOFQUY3hhWFzb8vACNHHkD/sqCwlThjHgDyjqOm+p/pN9RQYwUaJur1wBoPBFrhUHVZf7TvTvGw4Yq5yEFn3PUzJRJ6oSe/oEC/rwboUTnoeidCiDM3dnxUQZnWb7Iib0VsGWwDqqhAFXKsrqtd//AFEMZ2a+26hwhodfZBrRRoFAnTiHsiU2T+glVbRnUFAGdU3uqlDvuCDcFPh/t1b7bl/iXC7rCW0hKJST3dE9x6Iu6yqcWqACdfVAV01VtdEL2DVVDG2O3WHr7bhrRqTRQ4Q/aJOK2TJ8i75imQ+suybSVdzrmQvzX3ojU5cT4btHCifDdq00xw66MzTKPugUG9TKGOyNDUNsqBXVJU3la0ai7lyxFw0iCuOPFPPLgDuS2SUxoOiCr2Rd1USI72WzwH1clmOGFF+U0xsHU1wMWU0VXXMv0y7LI+6ruboOCpjvpgjdr44Thpsqjptb0xUwfUOSBVuc27ViqpzUUWoFOPXALoTjt6sONjfpCrDWxFstomye/wDG5pSyvQ+ypqJVE9ZXVk3BWQCEnjsjhCZ/xEu62HE03N1ZWOOI9tMgqcQVlSV5OTvfFCPVontNGm59KsAMRlbFWRoJAyeeyOLw5+mbvZO4G5oqN0xEFWMrFoV1Hd0YcbR8pImU/wB+L0lF+rLjjwf8plPPffW3YGzs2pZC91mlBhdTtY4fR2WZR391ZZpWGLapcIUFHLSqvZPA0Zlxhw1ChxeokRvrsWU0l3V1bGQsxVinxD+0Jz3ak13ETwzj9TUUd9ZaYMqAKJnpdNW0NDIJnh2/uu7csit5Jr2Gx32q9auZZllVHKqpgCHWkqmwCfEP23X6Dv8AHf2VzedVtBZsLGNVJfpN1Ou7qF34K6tK8wjFd6joqNRc9gDuyJOp4/nj2q2VeXLjbrXBaVXlWVjQKjfQPgOqug5WatVWLYLZZZg/n4LyRtU9FmJ9uFzKo4C3EZSs6ykH4RZa1WktVr/uX//EACoQAQACAgEEAgIBBQEBAQAAAAEAESExECBBUWEwcYGRoVCxwdHhQPDx/9oACAEBAAE/IX+jm47/APEJ0c60o2f+I3H5jKX0xKJ8M2lZZ/dIEZB3EH0F6M//AAJd/oh1CezLzf7ipaXPpv8AvLt8Uco4Papj9/Mbjv5FD8Fj/srb/wAb9IQKewqVKlcV1PXvYLl0Xs/xS4/KQ/PyG47+Ia/+E+5gh4pAAAUeDqqVKlSpUqVwliJhgAzfiFhb7Pb6PxG4/CYtDaP0IGOW3uvlea6alSpUqVK4rk2q+e3slvHhb+3wm47+BtX5T/iDiJQaD5q6K5Zkwke8cf2Fv/j4Dcd9bEbcD3f6hIQMAcJ98NSuHg+cAjpxTG8L13/66zcd9QVWZP8AFBdFoDscKhZnLBFfhlG13UHrqV8J+jUGXwXnXj11G+q2YKCIZdSBs4tLQTumKGo7w1AWYMitbQ0eINkWXOxwr5JUrqYDmn2JWKSvvpN9X9gxfKUo5v8AgT9hEbn1NJ3WLJFHZxpK7PEVbpAr3yoAgWmM4WpcN10PQyixGq9/+Ok307+i347o41iDtM11cN5zx6pj7UmINU8C0tlQjEI3S5U4cS/cePwobDMILfqWHPuCIOKlFOwhN/dsNbvFxN9DwQdCke5PONZ7dB0+0B/HeCypQmmbRkipSJ6qK7l+mDVzzlqirbV8GCVamgl6YKYBI43DQGsjuPLJ0le4w+kop9H3DWiY15qG2Oy4OnCcJxSjNX2dLyQdiENXBkdSgDO/zMkYZTRlubRWTuSmXOUYIow3BORbDeXalMdMXSIuYSFCEBdYKPuEA413LhoP4oD6A7gWGuHklMuNjAXQ75vA738TJiSueZ202n6+gmbkZYGqwYAahAvewwwvzDA3FvOXsbjuGIPQa4ETCLxGxKDtNRGxx0gVLzDllVv7j36HfJ7wgS74uhBikRlXRgWUC46v1ccFwSq21c+pBDK4qy4spmoldARpBiVwWR6eJZWr2IJuR/lleu3tDgyrtq/p5I75YjS0AjMIRLgHluWhl6ltfYwxx0GBS5Ux3e4tvF8sA95SZ8x4I6HmME7MTMIafUp4ajlo9qMkrNZOBQTCV1FzscfbyjPbq/q8kd84dKGYG6U0DClXTUcODXAjZeJYwukeCEZpSdx3iEFd5bf3V4Z4O4v5d5Zj2QGdLmHYaGEHvGIeSv1MK7zAomYsjxNyJMIuUmeN+4cdyP8AA5I74FpFEWAfxHyt+GZvI8y6HQu7nj1f0jqM7cLBlWy1cba3t2lPIPMJbsbBhv8AUV3fieglG3uHU4eI9oxjdNQbNGe6Q53crgijRAboznB28sWxvizNLP4hp+GuDcd8fyJ/8s1HK1HCFDSRF6TNppGMKLmEF5SlRo8x15kTukp12mLW0i85l4pMwIxwKhe3qDKpYtEsfceRlckAJf0JSlZ9xCaQn8Rn8pwbjvhUJ7fX8QUyQlVqMuZlNRccVx34FuXAjI/cGxMUthivNL0m2Y4Y8O4m5cXFcrIPbctaXzLrcs5ycPRDf4jseVeDcd8/Xc4YB/aCp7jwnKi8Kpc3K5GtTep26JT43eVBCDNxVWGaH6Z2R+YUBHh3APfO03+ByR3zfzbzrMviGgPLq2jwcEsdxpqVKlS6l8hBBHfDVMYqFgtbR81cAq7TBGlH8uSO+dg+AfxxpLrPGyuDiphHmublMqkOGXwrog5jAm3C9wniqlwyEXokAqCyoxK3csvroeb+tZ/54KVAuaYr93JHiw4IVBdT1RR2irUBj+8Kw3LwKihiLgwYlhZMGHEwiiu5ryy9bufcq5/rihW3iX8xh/z0PKG0ljBGbTv7lKjW2IrYMGEWYcI8DCIm0olD+YmXveGFIqz1CLCAAmU4bFCoZlFkMYbeHH2oIClmd1/fzGIty6HfR3gH/QiwXTvFizCXUciYh0nDZLh5X4myy9TcLUKJN2zMuS5sfPA1QtzK4CsAM+ZTeQmPbiExsl08/rdjpd9GxF5PJ3l4Y7jtxITSXNEXFBTnmrisPcjmftNPeYi6eOEYhFamFuZhEQd5ZYOYLisEsaBtgnHtFCtC25qQWvp0u+lFX3/pDnxrneWcgRZjhd4Y1xVRkamWGoblkv1F0DBkkW+I5IGZ2EJDNkF/pUyx1DB8Ns7HU76WLYTCYHQYzjDKp5KQXDVUuSpXCYIo0L7l3txnbRZeZgYRXwPEHjX7natDtY5d5L6nfVmoAs5qYwqhud+gg1KYRLgkleM1Ix4RdG5T3iLUqLhUaiJkuvsYp0Onjrd9f4U3wFzNDrgDlKlR6LUsAsQUqETBJHsIokemIdukNrSXmAyrGsn+4/A7+C9nUG9QM8QuEsVWo4+hKSuXhPDC0ZihtA+c2sXtL0SRqvqDYV5tmPdb4Tv4UcHXguOJip3Mtxa4YZk5cIGIkGmDl+C7sQmcykUaE847fCI7+JFZAMftKIynqG7vLMZiyp44wnBlwRlSpXDxSr6i3v4iO/kYyEv4U+SYWkwVxZh9Siw0wzlkeLI0MsDtF+QR38yK1TBVf5JoNINRlsBWlRfKPngXiFIprEU7+Yjv+jkd/wBI/9oADAMBAAIAAwAAABBzzzzzzzzgDDTzzzzzzyjzzzzzxSBhNTuUPTzzzyjzzzyF8n38fddfO+nzzyjTzyG0gGWn+ccx4rkDzyxzwRrIn6BffU2VnNLZHyjzwzfF9EwZBVW030/yryhzirSLBHFWRFNe89H7E2jRXN8Oo6P+32DGeJmhz+jwAJQPM1w9inAqeoiLhSjwVh0o3kP/AFLQUX25iv7o8BGeXrblsWkj6779Vjlo8paqvrvGbS+manfKafLoUlaQLvrSv6mAzxLi/Deoc8PYL2EML3lPdxY5GKcs88JEgVCEOf2ySbmWZU84888xat6GiTY+YqKNyc888888R9jrfiZkNRHSc88488888+lYbJ9fDG/8888o8888888e50kCl++++++oc8888888888888888888/8QAHxEBAQEAAwEAAwEBAAAAAAAAAQARECExIDBBUUBx/9oACAEDAQE/EP8AUJj+4FhYWJDLPxhth7Z95JZM/AGxg+cj5TTIZ9mDgR+dt+RpJnyO9lJu5ZnU+9ySBO7B7LGx+T+/kYbLGXrbvf0TJA1vJy1LfYGHeU0kzkjojvcx72elMsPfG28Cku9wNnBx56O2jeZUKeWbsqkZyWd5eNgJaMvXJGWnHr4ZjMdfA/cghy3XSUwpCOsfUhHcdsJ+HrgfuW22GVL/ALZPkdN+uNGMguknUufXB62eBsdcZzljZBO5FRkvfI5Hcus5EkjDkGyLqM9hv1GurafXP9lr8LSWuWWRw2a2hFwctkayjFEH0ByXdvAWzdGPLZJ7aGEiSLrEAlr9aSRB1Lls9yVsQi7Wv7GfYwtOj8Gl6gyHdl5MPxAQ/IT7fw/IOvh4HsGfmwkNizAH+z//xAAdEQEBAQADAQEBAQAAAAAAAAABABEQICExMEFA/9oACAECAQE/EP8AVsttttrgB/Tec4yxssht/IG85ZZZxlkeMO91vsFnO9NnbI8fwJwbHrISVKIXfeptsPVgs9n5Buy4y32S2eKz5LkPA+9WIPb4hwtVss6JJZEeVp0YWSbOjIC3IeFtycRqAyY8fHUdgsskmTyQ+2DLTYerZ70Syzgk8s42NvQy/t8dfqzz8FMm2UYT9j51GPLxsyyCT+WpEly9F/YMOvkt67DuTqYNWBESW+X12+Od4Jj7xm2TtpkYYTdg7fw5YOU0gjGfsAkJkitke/gIh4Hoh+yGzYLPyFIYemyyhLv66wodu1K8ZZZZZ/m//8QAKBABAAICAQQBBAMBAQEAAAAAAQARITFBEFFhcYEgMJGhQLHB0fBQ/9oACAEBAAE/ENv4Vy/4mj+EVNgPouCGG+X/ACIGS+X/ACboPZUr+Dom33K6NEzVqj26Jfo9hr+DH7gCN7sJ8Gf3CBNCKX5VgUjboX+QroqNFIiq4fCM2zYj/IITKtt79iQ5L9YY+v8AqImjJl/zZ+5U1mrdvQw/mVK+5o+6PJZyDT4N/CUdKC6D4G/mBlig4PgnvHhPWJZCkIRjKIFwKECeRgrbWg2nysfioi+Dg0Dw2fMRGvt6PtjuY5Qw91oJjP6RmTwcvl/EBsRQFAdg6ZmZUbioeH0ABKdphCAAIUiWJFje1TN5DXsjDY+8u+h+1omz9kgk+Y7hdvnRAO0YL+UF6UvUEqVHoZdL4RtxCnES9QjHaVDL3imV3NjBRY4K7dBP71Er7GibvsGVTa4T/PLmBHkDQNARuGSblRHmHQCVKISpUpyyiVKipTKqDYBCsDsSLWN5DuJ3/pEqH1aJs+siq1iUcenL8QnogFAGgJVzeQ2JogkgVKlQLgh1OldKiPVJXQixCwWJ2YgFWzXc7Du/UT6tU2fTcGtUKccDywL5zqAMTMDthLyNgB4zCIUvjzAxMBY5KdhcRCBX0EDqVKlQJUqJ0aqYqxEpGKVLdvyfk/r6tH0sfAKTjuvg3BWUiBShlf8AIRJyS4eNIWZPbTni4k7WQXqDCUBFE98RdqyVhYuWm4AI2MEajUuauDLAgqjqLZ7SkANSvHSqiQMDrSNGR0npibGU1gcJ4TP06PpIWNxFM8nyceiWKpG9whpsroL8D/qM5XD/ALijKDG5p9RQOCA3UFbDvCbIqD4g+ONgbWNNjStQINLZzZGkRfeNQTVQm1ykUC1gKmUq4inCXCVBEuVEgGDYtRGe/wC/6X9Oj6TbaYODK/GPmFUCCoAUEV1sFZ1EbJBzKhOVSXASAFO9Zl1jZooq6C4LJwp6g/cXRwxg4AAXQERGzs7RQGD3jwRM5YZSVsXbtgwChhHCd4CsO6jD3UDdtavnMKUBZOAY+0EaO8O6yCCyV5ldBmJCzrCWIUkQw/IMh+NfH0bESmvoBauXGjse3HxEGM1HxJXb2YYApgkFlsj0kWmJYXKxrXqkaWMUiAB2pEMtKXzFbXMGNBMDzBFe4GpUItCsBCfMfnkBubPmWXDDHcPF2ylLgGqGFY12h8qqJapUAbyVX4Ki0KJ3kWiAaYQFfNMeAtFnRcSBXcJ5JU7ESLf9xDK3+P8Ab0ehubPVJwwMqrRCGCWuWsvy3M16h2rRGOEcKSVVzAmxMRSdCB83FS9wwooHucTCuFw7TPMC1WyrBATQDIepiXGYjnJLle2IqXEVuVrUabVcuyVgSlxAotzs7yzC+rxH4KGN7W36KhoB3CxRlhCEIWdEtf3AmIUtUah+obR5g3z1MCssb4sq4dCHT3GupubuhAtAW3V6ftJRacQAuGGGDWOaiyaUVjmA+3ZbPMWqjleCHHu2iP8AMGXw1DkDGnA8wJlWta8xqqoKDbHYKXXqZ7S+IduyLgcGBVxEqLU56KBzFMLVwIjpmoWmBOYrVgiGu1/EcprhHAOv1G6TfIcdorLiXEgogUCc0YdP2B+epsmzoQNARU4BX+yAyG4lkBBLIT3ZGlhVLoF2MH64g4ZcsAV9XA4tMrvUR5UpfLDoEEdNO1jROHCcyyVcyyzAubILAdyhSRAaIlwLiNFRq2bLiKeIozBViQpYOFaN1Ez9VdQLi08EwLwaW1N1WoSgMmWpkXOEEIdkgOCy32fvrsTZ76EtaJ+baH8BOfPmG0S4ARjAO0CFyzlVEjU2lsNLNy75sNc44hTcD4VDnNVHkvQzqPY7YZYxAMsY1WQKcx7BSIfGZodwFLYlpQwGDAt1V3qAOihm7af1GRItlrmUSPiLWHghloAXHclimJSdNkVWrSi8EyFAKXn/AIQgq6rFzcGoIwExvihf1fXYmz30MsulRVZpS4F8BrN1Aeu9N7llE1AtkRRxBsxKaDoVAdxI0VZYg2XcUoWxuOdXi5jLFoUDwxo4aDwJBbBQVSAchgwNoUDQ4e0xNDhHJxDqAVhsbMMSUAgnCOIegxtO8LzAXoQMJwo1srU1xQUcBEcM4S6m24WA3G1IEsuhhKGgYaiEs1EuYhKGnV2SOGumxNnvpQu7UwxVEylJRtG4D7yocYG/YqNRgHNA9mo6U2hNzJUQbGpfu2x8BfiDw+an5RtuMZKIKDO1qFao8nDFCha0cxMtUcbGDWBFonDGDHvOoNaPuJHdLHclzsgkra2AxLaIqa8x+TbEIZiWQrGJwUDUQxVQNwQt4LWuhqqVelQwNKPh6aJs99P0H9wccWw/CKw2mcTfzgKRlFQcJaYKgQM3MdTA3LMYlLhCqZj1UQY3HOI+mtO2mN/R5MC0ose3iDRamJNjK4lI9mI5SGBU1hzEEFYGQU3B5AorcTbairXEZEthC0Cy4wktM4g11SVrvBrykCl0eIUEIAA3cFkB/wCLTP8AwO700TZ76XDsjLDKw3sSjEkaha47RSCCqOIislIy7CIrHLMFNSltKUXB1FcZ1HIrtlBAqO1xWiF+CoAiyIZY5lQGLSLDvGJFRcCmgUB2eYtrg2y3eNW2NdMLOI5S0NxmpAD6xMzIt0CG0RHQ5gqiFA/tmh6mF7uj0mY3rCe3pomz30NzKKq72Ff5GASmIYsdB4gi6GfuIrcroVKqUaiJKiLEW8xWFGU6VGVqVtQYVnmKuY22plbWHhKI6Y2ooCpUN0XAsEviLJdRtbYX2tC2ojoabNMyHZA+bB/cW1emxNnvql2MZui7P0x6NNG5n1COUGL56HRMxxF0OOrYgCodWLO/QwUgxQsUwLYFMsYtzNSYiEeoFqChA8FC2ODUwgfBtx8StAADEHDWjveR+Bj02Js99Ro4W+d8v8jFSexCYaoVzBNKQPz0EB7dHBFCGoqsERJniCHcoU5O0Dcqe0aglJDuIZMEHVGVbRPiKF0/iUOSo8MYhcsIogV8RYgMOAtrlrMB4kARy3ElbunXuGYAK4IeJibzQox7X8R30NzZ99c7RctHZ+6g0txyrKOkBV8wVDalWDZcLSaRUTKnqCG4itwI0QDDMvzavUWRRXiK72h1VdgS0AuqltpHtlh+N72RSgDlK5jWSGNRtJR4iqAjBUY0IF41CaIIpmiiEygA1iDpS0FtWiGNNIbqAhPZCzP7L+OpubPQilBycI2MNa0z0Ck/IxHjQEv5aoly5XlnkmCaxikou3UwYKaiMYNso1ZHsZItEp8MbW8gFUVzgzFlCtLDobwcmofWjF1AmjMA0h8SXFDwRWN8xxxiGtaUETzzA/8Ao4+9FI2mYlgF6uwYPlojWEi8qq9Tc2fQqe0a5pwH6Y52WiZLgqopmaWCaQqi+RAUjgjllTJLeYqbjGAxGEgPcBK52skPtTvwl1YGrlutCGCtpZVxm7m3YMwUEEFI0IpyXiMhHI6gCvLcCGkEqVYgTVss5QOo3LRTUOpGGHi/sc/EXqTd9DPNds2YHySqAxrsl1EMxruYN9oBtVRTP3oSVoi1CXUG4aKnHgd4w2l8wOg8GAAA7GahkA8NZiBjcarfCEFXziJhquJbb0gDRbQxgbUjG0pXmKY1SRdB3wai0CqpqXIUdUAFsfNaQ8HAfj6d30qDQbl13/1Fr0wIvRXTX7iYi4llKzN9FGkZwMYCsxFFUko1FPeUKK2XDsCDSFS5VlWQ0BHUR/HCwCZzsiFFzKbTKg2xctmcaLzKgFo/pKgAE5SC9Te0A9v9fVu+l6EaxOGN5rCn3EFuXK6MisxlHNzMUUSKQTmOJcZWMrlsyGWYUUqVwrfeWot/Mocuu8RDFYqFQUF1cN6aBw8xkis0XGFrGl1l0RWwxTvDCaKNJZPnQCPhgATYVS+uImdik39JubvqYhk5IS0MSA1qWYWFKnlgAm4KlLiSnoRS4ZpliKZRTmCvaoTapKY0OpebHuX5CfEDI2MYYuJscVxBopQVljXDmZhjJtCdl/2L9W762VK2hQE1A8S5UptKlBiMh2JiHYzwgnZCaTpfRBbY9JwFC47zNFJ7g9BiUoL8RG7cEABW24gKIttilNqG5iHYQoCUyEx37z47Rb+vd9jCKAIqxjEaHmFDS8wiBplly5tsjJdSph3EBaslLiWiVAqEFLiFVa7XiWXoeVwRyxKWkHpHY3UaRZQtmjmOp5wLiJPi7OCLzktohqEAKKacsW/sbvsVAKNnJKq2e0sqiCkDvLoVCM0jTKAivMorzKciI4JlGzTLLVKqtiWEDpYryxECqGhdRMcCjq4qZ+qOB1bDNZ5Gj4l/Y2m77QBKSNVouDgQuw5hVkVU0z7IYo9wSCQOkiWuRMriDdBBPEoLamhEvELOoCtRHaX1arzHbIPSIlSrtftbTd9yzCcmx+IAjtjg/EaLjIDkgaDmGFjUVYcpmAszGsSydhCGJlEuVMR3gPmCEze83y19zabvvBEhyNQAVD2WUC05RuWoROUgawfmYZTsWW1qHuANfzL6yxOF+2PZU8RC0vz97abv4Ny+l/w9ps/+R//Z");


                this.$router.push({ name: 'slider' });
                //if (this.$cookies.get("steadyforumsessionid")) {
                //    fetch('/api/Details/' + this.$cookies.get("steadyforumsessionid"))
                //        .then(response => {
                //            if (response.status == 400) {
                //                this.isloginshow = true;
                //            } else if (response.status == 200) {
                //                this.isloginshow = false;
                //            }
                //            return;
                //    })
                //}

                //if (!this.userLoginInput) { return }

                //var userCredBase64 = btoa(unescape(encodeURIComponent(this.userLoginInput + this.userPasswordInput)));
                //var userCredSha256 = await this.sha256(userCredBase64)


                //fetch('/api/Login/' + this.userLoginInput + "/" + userCredSha256)
                //    .then(r => r.json())
                //    .then(json => {
                //        this.$cookies.remove('steadyforumname');
                //        this.$cookies.set("steadyforumname", this.userLoginInput, "expiring time");
                //        this.$cookies.remove('steadyforumsessionid');
                //        this.$cookies.set("steadyforumsessionid", json.sessionid, "expiring time")
                //        this.$router.push({ name: 'slider' });  /*instead this.isloginshow = false;*/                        
                //        return;
                //    });
            },
            requestloadercolor() {
                fetch('/api/LoginColor/' + this.sessionId)
                    .then(r => r.json())
                    .then(json => {
                        this.loadercolor = json.loadercolor;
                        return;
                    });
            },
            searchPost() {

            }
        },
    });
</script>

<style scoped>
    .loginWrapper {
        background: #0d0d0dfc;
        /*padding-top: 20em;*/
        height: 100%;
        z-index: 20;
        position: absolute;
        margin: auto;
        left: 0;
        right: 0;
        text-align: center;
        justify-content: center;
        flex-direction: column;
        align-items: center;
        display: flex
    }

    .isclosed {
        display: none;
        /*transition*/
    }

    #loginName {
        background: none;
        height: 3em;
        color: white;
        outline: 2px solid #fff;
        border-radius: 5px;
    }

    #loginPassword {
        background: none;
        height: 3em;
        color: white;
        outline: 2px solid #fff;
        border-radius: 5px;
    }

    #loginBtn {
        margin-top: 0.2em;
        background: none;
        border: none;
        font-size: 1.2em;
        color: #00ffc4;
    }

    #loginBtn:active {
        background: gray;
        transition: 0.2s ease-in;
    }

    .loader {
        position: absolute;
        top: calc(10% - 32px);
        left: calc(10% - 32px);
        width: 64px;
        height: 64px;
        border-radius: 50%;
        perspective: 800px;
    }

    .inner {
        position: absolute;
        box-sizing: border-box;
        width: 100%;
        height: 100%;
        border-radius: 50%;
    }

        .inner.one {
            left: 0%;
            top: 0%;
            animation: rotate-one 1s linear infinite;
            border-bottom: 3px solid #EFEFFA;
        }

        .inner.two {
            right: 0%;
            top: 0%;
            animation: rotate-two 1s linear infinite;
            border-right: 3px solid #EFEFFA;
        }

        .inner.three {
            right: 0%;
            bottom: 0%;
            animation: rotate-three 1s linear infinite;
            border-top: 3px solid #EFEFFA;
        }

    @keyframes rotate-one {
        0% {
            transform: rotateX(35deg) rotateY(-45deg) rotateZ(0deg);
        }

        100% {
            transform: rotateX(35deg) rotateY(-45deg) rotateZ(360deg);
        }
    }

    @keyframes rotate-two {
        0% {
            transform: rotateX(50deg) rotateY(10deg) rotateZ(0deg);
        }

        100% {
            transform: rotateX(50deg) rotateY(10deg) rotateZ(360deg);
        }
    }

    @keyframes rotate-three {
        0% {
            transform: rotateX(35deg) rotateY(55deg) rotateZ(0deg);
        }

        100% {
            transform: rotateX(35deg) rotateY(55deg) rotateZ(360deg);
        }
    }
</style>